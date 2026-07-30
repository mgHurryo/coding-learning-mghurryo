---
title: JSON 类型
description: 讲解 MySQL 8.4 JSON 字段的二进制存储, 结构约束, 路径查询, JSON_TABLE, 部分更新与索引设计.
tags:
  - MySQL
  - Basics
  - DataType
  - JSON
category: MySQL
version: "8.4 LTS"
---

# MySQL JSON 类型

本文以 MySQL 8.4 LTS 为基线. JSON 类型适合保存结构可演进, 属性较稀疏, 但仍需要由 SQL 查询的文档数据. 它应当补充关系模型, 而不是替代表的主键, 外键, 金额, 库存等稳定业务字段.

## 类型作用

`JSON` 是 MySQL 的原生文档类型. 写入时 MySQL 会验证 JSON 语法并转换为内部二进制格式, 因而读取成员时通常不必每次重新解析整段文本.

典型用途包括:

- 商品的可选规格, 不同品类拥有不同属性.
- 第三方 API 的扩展字段, 但核心标识仍单独建列.
- 事件或审计记录中的附加上下文.
- 配置快照, 前提是文档体积受控且更新频率不高.

不适合直接放入 JSON 的数据包括:

- 主键, 外键, 唯一业务编号和高频 JOIN 键.
- 需要精确约束的金额, 库存, 状态机和权限关系.
- 高频排序, 分组或范围查询的核心字段.
- 无上限增长的日志数组, 聊天记录或二进制文件.

判断原则是: 稳定且重要的属性建普通列, 稀疏且经常扩展的属性放 JSON. 如果某个 JSON 路径成为高频条件, 应提升为普通列, generated column 或函数索引.

## 存储与字段约束

### 原生二进制存储

JSON 列不是普通 `TEXT`. MySQL 在写入时检查文档, 规范化内部表示, 并以便于成员访问的二进制格式保存. 文档仍受行大小, `max_allowed_packet`, redo/undo, binlog 和备份成本约束. JSON 键名会重复占用空间, 因此大文档和重复数组并不免费.

原生 JSON 列天然拒绝无效 JSON:

```sql
CREATE TABLE json_validation_demo (
    payload JSON NOT NULL
);

INSERT INTO json_validation_demo (payload)
VALUES ('{"ok": true}');

-- 下面的字符串缺少右花括号, 写入会失败.
INSERT INTO json_validation_demo (payload)
VALUES ('{"ok": true');
```

因此对原生 JSON 列写 `CHECK (JSON_VALID(payload))` 通常是重复约束. `JSON_VALID()` 更适合检查导入阶段的 `TEXT` 或 `VARCHAR` 原始数据.

### NULL 与默认值

SQL `NULL` 和 JSON 文档中的 `null` 不是同一概念:

- SQL `NULL` 表示整列没有值.
- JSON `null` 是文档中的一个合法 JSON 值.
- 路径不存在时, 多数提取函数返回 SQL `NULL`.

如果业务要求文档始终存在, 使用 `NOT NULL`. MySQL 8.4 中 JSON 默认值要写成表达式:

```sql
CREATE TABLE json_default_demo (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
    metadata JSON NOT NULL DEFAULT (JSON_OBJECT())
);
```

### CHECK 的三值逻辑

MySQL 的 `CHECK` 约束在表达式为 `FALSE` 时拒绝数据, 为 `TRUE` 或 `UNKNOWN` 时通过. 路径缺失常产生 SQL `NULL`, 也就是 `UNKNOWN`. 所以只检查成员类型可能无法阻止成员缺失, 必须同时检查路径存在.

下面的建表示例把稳定字段关系化, 把可变规格放入 JSON, 并显式约束文档形状:

```sql
CREATE TABLE product_catalog (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    sku VARCHAR(64) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    stock INT UNSIGNED NOT NULL DEFAULT 0,
    attributes JSON NOT NULL,

    brand VARCHAR(64)
        GENERATED ALWAYS AS (
            JSON_UNQUOTE(JSON_EXTRACT(attributes, '$.brand'))
        ) STORED,

    model VARCHAR(64)
        GENERATED ALWAYS AS (
            JSON_VALUE(attributes, '$.model' RETURNING CHAR(64))
        ) VIRTUAL,

    created_at TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3),
    updated_at TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3)
        ON UPDATE CURRENT_TIMESTAMP(3),

    PRIMARY KEY (id),
    UNIQUE KEY uk_product_catalog_sku (sku),
    KEY idx_product_catalog_brand (brand),
    KEY idx_product_catalog_model (model),

    CONSTRAINT chk_product_price
        CHECK (price >= 0),
    CONSTRAINT chk_attributes_object
        CHECK (JSON_TYPE(attributes) = 'OBJECT'),
    CONSTRAINT chk_attributes_brand_exists
        CHECK (JSON_CONTAINS_PATH(attributes, 'one', '$.brand') = 1),
    CONSTRAINT chk_attributes_brand_string
        CHECK (JSON_TYPE(JSON_EXTRACT(attributes, '$.brand')) = 'STRING')
) ENGINE = InnoDB
  DEFAULT CHARACTER SET = utf8mb4
  COLLATE = utf8mb4_0900_ai_ci;
```

这里的三个 JSON 约束各自负责一件事:

1. 根节点必须是对象, 不能是数组或标量.
2. `brand` 路径必须存在.
3. `brand` 的 JSON 类型必须是字符串.

复杂文档还可以在应用入口使用 JSON Schema 校验, 或研究 `JSON_SCHEMA_VALID()`. 但数据库约束应保持可理解和可维护, 不要把整个业务对象模型压入一个难以演进的巨大 `CHECK`.

## 可执行写入示例

```sql
INSERT INTO product_catalog
    (sku, price, stock, attributes)
VALUES
(
    'KB-1001',
    699.00,
    25,
    JSON_OBJECT(
        'brand', 'KeyLab',
        'model', 'K84',
        'color', 'black',
        'dimensions', JSON_OBJECT(
            'width_mm', 320,
            'height_mm', 42,
            'weight_g', 780.5
        ),
        'wireless', JSON_OBJECT(
            'enabled', TRUE,
            'latency_ms', 2.4
        ),
        'tag_ids', JSON_ARRAY(10, 20)
    )
),
(
    'MS-2001',
    259.00,
    80,
    JSON_OBJECT(
        'brand', 'PointWorks',
        'model', 'M2',
        'color', 'white',
        'dpi', JSON_ARRAY(800, 1600, 3200),
        'tag_ids', JSON_ARRAY(20, 30)
    )
);
```

`JSON_OBJECT()` 和 `JSON_ARRAY()` 能避免应用自行拼接引号与转义字符. 从应用写入时仍应使用预编译参数, 不要把用户输入直接拼入 JSON 文本或 JSON 路径.

## 路径读取与类型转换

### ->, ->> 与 JSON_VALUE

`->` 返回 JSON 值, 字符串标量仍带 JSON 引号. `->>` 等价于对提取结果再执行 `JSON_UNQUOTE()`, 更适合展示和字符串比较.

```sql
SELECT
    sku,
    attributes->'$.dimensions' AS dimensions_json,
    attributes->>'$.brand' AS brand_text,
    JSON_VALUE(
        attributes,
        '$.dimensions.weight_g' RETURNING DECIMAL(10, 2)
    ) AS weight_g
FROM product_catalog
WHERE attributes->>'$.color' = 'black';
```

关键差异:

- `attributes->'$.brand'` 的结果是 JSON 字符串 `"KeyLab"`.
- `attributes->>'$.brand'` 的结果是 SQL 字符串 `KeyLab`.
- `JSON_VALUE(... RETURNING DECIMAL(10, 2))` 能直接得到带目标类型的标量, 适合数值比较和 generated column.
- 路径可能缺失时, 要明确业务希望返回 `NULL`, 默认值还是报错, 并按 `JSON_VALUE` 的 `ON EMPTY` 和 `ON ERROR` 语义设计.

不要用 `->` 的带引号结果直接和普通字符串比较. 也不要在每条高频查询里重复转换同一路径而完全不建索引.

### JSON_TABLE 将数组关系化

`JSON_TABLE()` 把数组成员映射成关系行, 适合继续 JOIN, 过滤或聚合:

```sql
SELECT
    p.id,
    p.sku,
    jt.tag_id
FROM product_catalog AS p
JOIN JSON_TABLE(
    p.attributes,
    '$.tag_ids[*]'
    COLUMNS (
        tag_id BIGINT UNSIGNED PATH '$' ERROR ON ERROR
    )
) AS jt ON TRUE
ORDER BY p.id, jt.tag_id;
```

它适合读取和报表展开, 但不会把数组变成真正的关联表. 如果标签需要外键, 唯一约束, 独立生命周期或高频双向查询, 应使用 `product_tag(product_id, tag_id)` 关联表.

## 更新与部分更新

使用 JSON 修改函数可以在一条 SQL 中原子地修改路径:

```sql
UPDATE product_catalog
SET attributes = JSON_SET(
        attributes,
        '$.firmware.version', '1.2.1',
        '$.wireless.latency_ms', 1.8
    )
WHERE sku = 'KB-1001';

UPDATE product_catalog
SET attributes = JSON_REMOVE(
        attributes,
        '$.deprecated_property'
    )
WHERE sku = 'KB-1001';
```

`JSON_SET()` 会新增或替换路径, `JSON_REPLACE()` 只替换已存在路径, `JSON_INSERT()` 只新增不存在路径, `JSON_REMOVE()` 删除路径.

对 InnoDB 的原生 JSON 列, MySQL 在满足条件时可对 `JSON_SET()`, `JSON_REPLACE()` 或 `JSON_REMOVE()` 执行原地部分更新, 从而避免重写完整文档. 这是优化机会, 不是每条语句都保证发生. 新值增长需要可复用空间, 表达式必须直接基于原列, 具体限制应按 8.4 手册确认.

可以观察文档占用和部分更新留下的可复用空间:

```sql
SELECT
    sku,
    JSON_STORAGE_SIZE(attributes) AS storage_bytes,
    JSON_STORAGE_FREE(attributes) AS reusable_bytes
FROM product_catalog;
```

行锁仍然作用于整行, 不存在 JSON 成员级锁. 应用先读取完整文档, 在内存修改, 再覆盖整列时仍可能产生丢失更新. 优先使用单条 `JSON_SET()`, 或配合版本号做乐观锁.

## 索引方案

JSON 列不能像普通标量列那样直接建立常规 B-tree 索引. 应根据访问模式选择以下方案.

### 方案一: generated column

前面的 `brand` 和 `model` 把固定路径转换成有明确 SQL 类型的列, 再建立普通索引. 优点是列可见, 容易用于查询, 统计和排障. `STORED` 占用表空间但读取直接, `VIRTUAL` 不保存值但计算结果可由 InnoDB 建索引.

```sql
EXPLAIN
SELECT id, sku, price
FROM product_catalog
WHERE brand = 'KeyLab';
```

### 方案二: 函数索引

只需要按表达式查找且不想暴露 generated column 时, 可以建立函数索引. JSON 提取结果往往是长文本类型, 因此要 `CAST` 成可索引的有界标量:

```sql
CREATE INDEX idx_product_catalog_color
ON product_catalog (
    (CAST(attributes->>'$.color' AS CHAR(32)))
);

SELECT id, sku
FROM product_catalog
WHERE CAST(attributes->>'$.color' AS CHAR(32)) = 'black';
```

查询表达式必须与索引表达式在语义和类型上匹配. 路径, `CAST` 长度, 字符集或排序规则不一致都可能使优化器无法匹配该索引.

### 方案三: JSON 数组多值索引

对标量数组的成员查询, MySQL 8.4 可以使用 multi-valued index:

```sql
CREATE INDEX idx_product_catalog_tag_ids
ON product_catalog (
    (CAST(attributes->'$.tag_ids' AS UNSIGNED ARRAY))
);

SELECT id, sku
FROM product_catalog
WHERE 20 MEMBER OF (attributes->'$.tag_ids');
```

多值索引适合 `MEMBER OF()`, `JSON_CONTAINS()` 和部分 `JSON_OVERLAPS()` 成员查询. 它不提供关系表的外键, 行级属性和完整约束. 数组成员很多, 重复率高或更新频繁时, 索引体积与写放大会显著增加.

## 适用与不适用场景

| 场景 | 建议 | 原因 |
| :--- | :--- | :--- |
| 不同商品拥有少量不同规格 | 适用 | 属性稀疏, 结构会演进 |
| 保存第三方响应中的非核心扩展字段 | 适用 | 可保留原始结构, 核心字段仍可关系化 |
| 小型配置快照 | 有条件适用 | 需要限制大小并明确版本 |
| 订单金额与库存 | 不适用 | 需要精确类型, 约束, 锁和高频运算 |
| 用户角色与权限 | 不适用 | 需要外键, 唯一性和多方向查询 |
| 无限追加的事件数组 | 不适用 | 单行持续膨胀, 更新和恢复成本高 |
| 高频 GROUP BY 或范围统计字段 | 通常不适用 | 普通列和 B-tree 索引更稳定 |

## 常见错误

1. 用 `TEXT` 保存 JSON, 然后在应用中假定它始终合法.
2. 把所有列合并成一个 JSON 文档, 丢失类型, 唯一键和外键约束.
3. 误把 SQL `NULL`, JSON `null` 和路径不存在视为同一种状态.
4. 只写 `CHECK (JSON_TYPE(...))`, 忽略 `UNKNOWN` 会通过 CHECK.
5. 用 `->` 返回的 JSON 字符串与普通字符串直接比较.
6. 在 WHERE 中反复提取和转换路径, 却没有 generated column 或函数索引.
7. 函数索引没有 `CAST` 到有界标量, 或查询表达式与索引表达式不一致.
8. 把 JSON 数组当作关联表, 却又需要外键, 去重和独立更新.
9. 通过应用读出并覆盖整份文档, 导致并发更新互相覆盖.
10. 让单个文档无上限增长, 忽略 redo, undo, binlog, 备份和复制成本.

## 设计检查清单

- 根节点是对象, 数组还是标量, 是否由 CHECK 明确.
- 必需路径是否显式检查存在, 类型是否检查.
- 稳定核心字段是否已经关系化.
- 高频路径是否有 generated column, 函数索引或多值索引.
- JSON 路径缺失, JSON null 和 SQL NULL 的行为是否明确.
- 更新是否使用单条 JSON 修改函数, 是否存在丢失更新风险.
- 文档大小, 数组长度和保留周期是否有上限.
- 备份, 复制和日志系统能否承担大文档变更.

## 相关主题

- [DataType 数据类型](DataType-数据类型.md)
- [Column 字段](Column-字段.md)
- [CREATE TABLE 创建表](CREATE-TABLE-创建表.md)

## 参考资料

- [MySQL 8.4 Reference Manual: The JSON Data Type](https://dev.mysql.com/doc/refman/8.4/en/json.html)
- [MySQL 8.4 Reference Manual: JSON Function Reference](https://dev.mysql.com/doc/refman/8.4/en/json-function-reference.html)
- [MySQL 8.4 Reference Manual: Functions That Search JSON Values](https://dev.mysql.com/doc/refman/8.4/en/json-search-functions.html)
- [MySQL 8.4 Reference Manual: JSON Table Functions](https://dev.mysql.com/doc/refman/8.4/en/json-table-functions.html)
- [MySQL 8.4 Reference Manual: Partial Updates of JSON Values](https://dev.mysql.com/doc/refman/8.4/en/json.html#json-partial-updates)
- [MySQL 8.4 Reference Manual: CREATE TABLE CHECK Constraints](https://dev.mysql.com/doc/refman/8.4/en/create-table-check-constraints.html)
- [MySQL 8.4 Reference Manual: Multi-Valued Indexes](https://dev.mysql.com/doc/refman/8.4/en/create-index.html#create-index-multi-valued)
