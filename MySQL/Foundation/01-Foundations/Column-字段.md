---
title: Column 字段
description: 系统说明 MySQL 字段的命名、类型、空值、默认值、生成规则、约束、索引影响和演进方法。
tags:
  - MySQL
  - Basics
  - Column
  - Schema-Design
category: MySQL
version: MySQL-8.4-LTS
---

# Column 字段

> 字段是表中一项原子事实的 schema 契约。一个可靠字段必须同时表达“存什么、允许什么、缺失时怎样处理、如何比较、怎样被访问”。

## 1. 字段定义由什么组成
~~~sql
column_name data_type
    [UNSIGNED]
    [CHARACTER SET charset_name]
    [COLLATE collation_name]
    [NULL | NOT NULL]
    [DEFAULT literal | DEFAULT (expression)]
    [GENERATED ALWAYS AS (expression) VIRTUAL | STORED]
    [AUTO_INCREMENT]
    [ON UPDATE CURRENT_TIMESTAMP[(fsp)]]
    [VISIBLE | INVISIBLE]
    [COMMENT 'business meaning']
~~~

非字面量默认表达式通常必须放在括号中；`TIMESTAMP` / `DATETIME` 的 `CURRENT_TIMESTAMP[(fsp)]` 是专门规则。自动更新子句也只适用于这两类时间字段，并不是可填任意表达式的通用钩子。默认表达式还受函数种类、列依赖顺序等限制，建表前应对照 MySQL 8.4 文档验证。

约束可以写在字段后，也可以写成表级约束：

~~~sql
PRIMARY KEY (column_list)
UNIQUE KEY index_name (column_list)
FOREIGN KEY (column_list) REFERENCES parent_table (column_list)
CHECK (boolean_expression)
~~~

字段类型决定可表示的值；属性和约束决定哪些值合法；索引决定常见访问路径的成本。三者不能互相替代。

## 2. 命名与原子性

字段名应稳定表达业务事实，并把单位写进名字或注释：

| 推荐 | 不推荐 | 原因 |
| :--- | :--- | :--- |
| `total_amount_cent` | `money` | 单位和含义明确 |
| `temperature_celsius` | `value` | 不会混淆摄氏/华氏 |
| `created_at` | `time` | 明确是创建事件 |
| `is_deleted` | `flag1` | 读者能判断状态含义 |
| `shipping_address_id` | `data` | 明确是引用关系 |

不要在单列中保存需要独立过滤、约束或连接的多个事实，例如 `"phone,email,wechat"`、逗号标签或拼接地址。它们应拆列、建子表，或在确属稀疏扩展属性时使用受控 JSON。

## 3. 类型、长度与单位

字段的类型选择以业务域为起点：

- 数量和编号：整数；根据未来上限选择字节数。
- 金额和精确比例：`DECIMAL`，或有严格单位协议的最小货币单位整数。
- 连续测量：通常 `DOUBLE`，并记录单位和测量精度。
- 人类文本：`VARCHAR` / `TEXT` + 明确 collation。
- 哈希、密文和原始字节：`BINARY` / `VARBINARY` / `BLOB`。
- 日期、日历时间、时间点和时长：分别选择 `DATE`、`DATETIME` / `TIMESTAMP`、`TIME`。
- 稀疏文档和空间对象：分别使用 `JSON` 和空间类型，并补索引策略。

`VARCHAR(64)` 的 64 是字符数；`VARBINARY(64)` 的 64 是字节数；`DECIMAL(12,2)` 的 12 是总数字位数；`DATETIME(6)` 的 6 是小数秒位数。详见：[DataType 数据类型](DataType-数据类型.md)。

## 4. NULL、空值和三值逻辑

`NULL` 表示未知或不适用，不等于空字符串、0、`FALSE`、JSON `null` 或“1970-01-01”。

~~~sql
CREATE TEMPORARY TABLE null_lab (
    id INT PRIMARY KEY,
    discount_amount DECIMAL(10,2) NULL
);

INSERT INTO null_lab VALUES
    (1, NULL),
    (2, 0.00),
    (3, 5.00);

SELECT
    COUNT(*) AS row_count,
    COUNT(discount_amount) AS known_discount_count,
    SUM(discount_amount) AS known_discount_sum
FROM null_lab;
~~~

结果中 `COUNT(*)` 统计 3 行，`COUNT(discount_amount)` 只统计 2 个非 NULL 值。判断空值必须使用 `IS NULL` / `IS NOT NULL`，不能写 `= NULL`。

选型原则：

- 业务必填且存在合理初始值：`NOT NULL` + 明确 `DEFAULT`。
- 确实存在“未知/不适用”：允许 `NULL`，并在查询、唯一性和聚合中处理三值逻辑。
- 不要为了减少 `NULL` 而用空字符串或魔法数字伪造未知值。

## 5. DEFAULT 的实际行为
在 INSERT 中省略某列或显式使用 `DEFAULT` 关键字时，MySQL 使用该列默认值；UPDATE 也可以用 `SET column_name = DEFAULT` 把列恢复为默认值。显式写 `NULL` 是一次 NULL 赋值，不等于请求默认值：`NOT NULL` 列在严格模式下会报错，非严格模式可能按类型规则转换并产生 warning，不能依赖这种兼容行为。

~~~sql
CREATE TEMPORARY TABLE default_lab (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    status TINYINT NOT NULL DEFAULT 1,
    created_at TIMESTAMP(6) NOT NULL DEFAULT CURRENT_TIMESTAMP(6),
    options JSON NOT NULL DEFAULT (JSON_OBJECT())
);

INSERT INTO default_lab () VALUES ();
INSERT INTO default_lab (status) VALUES (DEFAULT);

UPDATE default_lab
SET status = DEFAULT
WHERE id = 1;
~~~

字面量可直接写成 `DEFAULT 1`；JSON、BLOB、TEXT 等类型的默认值和非字面量默认表达式使用 `DEFAULT (expression)` 形式，并受允许函数与列引用顺序限制。默认值应表达无歧义的初始状态，不能用 `DEFAULT ''` 掩盖必填文本，也不能把“当前操作者”“动态汇率”等业务上下文伪造成数据库默认值。

## 6. 常用字段属性
| 属性 | 作用 | 重要边界 |
| :--- | :--- | :--- |
| `NOT NULL` | 拒绝 SQL NULL | 不会拒绝空字符串、0 或空 JSON |
| `UNSIGNED` | 取消整数负区间 | 子外键两侧类型必须兼容；应用语言可能无无符号整数 |
| `AUTO_INCREMENT` | 自动分配整数 | 适合内部代理主键，不保证连续无缺口 |
| `CHARACTER SET` | 决定字符编码 | `utf8mb4` 才完整支持 Unicode 和 emoji |
| `COLLATE` | 决定字符串相等和排序 | 会直接影响 UNIQUE 的冲突判定 |
| `COMMENT` | 记录单位、状态域和用途 | 注释不是约束，仍需 `CHECK` / FK |
| `ON UPDATE CURRENT_TIMESTAMP` | 时间列在行更新时自动刷新 | 只适用于 `TIMESTAMP` / `DATETIME`，也不是完整审计日志 |
| `VISIBLE` / `INVISIBLE` | 控制隐式列可见性 | invisible 列不在 `SELECT *` 中，显式列名仍可访问 |
| `SRID` | 限制空间字段的坐标参考系统 | 插入和查询对象必须使用兼容 SRID |

`AUTO_INCREMENT` 只保证生成唯一候选值，不保证事务回滚后补回编号，也不保证业务连续性。发票号等法律或业务序号需要单独的并发分配规则。

## 7. Generated Column：派生值只定义一次
~~~sql
CREATE TABLE contact (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    email VARCHAR(254) NOT NULL,
    email_domain VARCHAR(253)
        GENERATED ALWAYS AS (SUBSTRING_INDEX(email, '@', -1)) STORED,
    profile JSON NOT NULL,
    country_code CHAR(2)
        GENERATED ALWAYS AS (
            JSON_UNQUOTE(JSON_EXTRACT(profile, '$.countryCode'))
        ) VIRTUAL,
    PRIMARY KEY (id),
    KEY idx_contact_email_domain (email_domain),
    KEY idx_contact_country_code (country_code)
) ENGINE=InnoDB;
~~~

- 未索引的 `VIRTUAL` 值不存入聚簇行，读取时计算。
- 被索引的 `VIRTUAL` 值会物化到 InnoDB 二级索引中，并在写入时维护，因此仍有索引空间和写放大成本。
- `STORED` 值在写入时计算并保存在聚簇行中，适合读取频繁、计算较重或需要稳定物化的表达式。
- generated column 只能表达确定的行内派生规则，不能替代跨表业务计算。
- 索引表达式必须与查询表达式兼容；最终用 `EXPLAIN` 验证。

## 8. 约束的作用
| 约束 | 保证什么 | 不能保证什么 |
| :--- | :--- | :--- |
| `PRIMARY KEY` | 每行有唯一且非 NULL 的身份 | 不保证业务 ID 适合对外暴露 |
| `UNIQUE` | 非 NULL 的单列或组合键不重复 | MySQL 允许多个 NULL；比较结果还受 collation 影响 |
| `FOREIGN KEY` | 子表的非 NULL 引用目标存在 | 不自动解决跨服务、一致性和删除策略 |
| `CHECK` | 条件结果不能为 FALSE | UNKNOWN 会通过，因此必填输入还需要 `NOT NULL` |
| `NOT NULL` | 字段不是 SQL NULL | 不保证非空字符串或业务有效值 |

~~~sql
CREATE TABLE product (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    sku VARCHAR(64) CHARACTER SET ascii COLLATE ascii_bin NOT NULL,
    unit_price DECIMAL(12,2) NOT NULL,
    status TINYINT UNSIGNED NOT NULL DEFAULT 1,
    PRIMARY KEY (id),
    UNIQUE KEY uk_product_sku (sku),
    CHECK (unit_price >= 0),
    CHECK (status IN (0, 1))
) ENGINE=InnoDB;
~~~

这里 `ascii_bin` 让 SKU 按字节、区分大小写比较；如果业务认为 `abc-1` 与 `ABC-1` 相同，就应选择大小写不敏感的规则并用测试数据验证 UNIQUE 行为。

## 9. 完整示例：订单明细
~~~sql
CREATE TABLE order_line (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT
        COMMENT '内部主键',
    order_id BIGINT UNSIGNED NOT NULL
        COMMENT '所属订单',
    product_id BIGINT UNSIGNED NOT NULL
        COMMENT '商品快照对应的商品 ID',
    quantity INT UNSIGNED NOT NULL
        COMMENT '购买数量，单位=件，业务上限=1000000',
    unit_price DECIMAL(12,2) NOT NULL
        COMMENT '下单时单价，单位=CNY',
    discount_amount DECIMAL(18,2) NOT NULL DEFAULT 0.00
        COMMENT '该明细优惠金额，单位=CNY',
    payable_amount DECIMAL(18,2)
        GENERATED ALWAYS AS (
          quantity * unit_price - discount_amount
        ) STORED,
    note VARCHAR(500) NULL
        COMMENT '可选买家备注',
    created_at TIMESTAMP(6) NOT NULL DEFAULT CURRENT_TIMESTAMP(6),
    PRIMARY KEY (id),
    UNIQUE KEY uk_order_line_product (order_id, product_id),
    KEY idx_order_line_product (product_id),
    CHECK (quantity BETWEEN 1 AND 1000000),
    CHECK (unit_price >= 0),
    CHECK (discount_amount >= 0),
    CHECK (discount_amount <= quantity * unit_price)
) ENGINE=InnoDB;
~~~

`quantity` 的业务上限使 `quantity * unit_price` 最多有 16 位整数，`DECIMAL(18,2)` 因而能够容纳合法的应付金额。只声明 generated column 的目标类型而不证明中间结果上限，可能在极端值上溢出。

### 9.1 逐字段解释

| 字段 | 用法和作用 | 关键选择 |
| :--- | :--- | :--- |
| `id` | 作为稳定、窄的聚簇索引键 | 只做身份，不承载业务顺序 |
| `order_id` | 连接订单头并支持按订单读取明细 | 与父表主键的类型和 signedness 必须一致 |
| `product_id` | 连接商品并支持商品维度分析 | 索引服务反向查询，是否外键由一致性边界决定 |
| `quantity` | 参与精确乘法的非负整数 | `CHECK` 同时定义下限和容量推导所需的上限 |
| `unit_price` | 保存下单时价格快照 | 不能只连接商品当前价，否则历史订单会漂移 |
| `discount_amount` | 无优惠时有明确的 0 默认值 | 精度覆盖订单行最大合法总价，`NOT NULL` 简化计算 |
| `payable_amount` | 由其他列确定的派生结果 | 先证明精度上限，再决定 STORED 和是否需要索引 |
| `note` | “没有备注”有明确的 NULL 语义 | 不参与高频过滤，不应建普通索引 |
| `created_at` | 记录插入事件时间点 | 微秒精度需与应用驱动配置一致 |

生产设计还应决定币种。多币种场景可增加 `currency_code CHAR(3)`，并用币种规则约束小数位；只写“金额”而不写币种和单位是不完整的 schema。

## 10. 字段如何影响索引和存储

1. InnoDB 主键值会出现在每个二级索引叶子中，主键越宽，所有二级索引越大。
2. 很长的 `VARCHAR`、`TEXT` 和 `BLOB` 可能使用页外存储，但不是“这些类型永远完全行外”；行格式和值长度会影响实际布局。
3. 字符串索引按编码后的字节占空间，`utf8mb4 VARCHAR(255)` 的最坏字节数远大于 ASCII。
4. nullable 字段会引入空值状态；更重要的是查询必须正确处理三值逻辑，不能只从节省字节判断是否允许 NULL。
5. 在索引列上做隐式类型转换、字符集转换或不匹配函数，可能改变访问路径。
6. 列声明只是起点，最终需要通过 [EXPLAIN 使用方法](EXPLAIN-使用方法.md) 和真实数据分布验证。

## 11. 字段演进规则

修改大表字段前至少检查：

- 新旧类型是否能无损转换，是否存在越界、截断、非法日期或非法字符。
- 应用是否先兼容新旧 schema，是否存在滚动发布窗口。
- 增加 `NOT NULL` 前，历史 NULL 是否已回填且有新写入防线。
- 缩短字符串或 DECIMAL 精度前，是否完成最大值和分位数扫描。
- 修改 collation 是否会让原本不同的值在 UNIQUE 下冲突。
- generated column、索引和外键重建会消耗多少时间、空间和日志。
- DDL 使用 `INSTANT`、`INPLACE` 还是 `COPY`，是否会阻塞写入。
- 是否准备校验 SQL、回滚方案和备份恢复路径。

详见：[ALTER TABLE 修改表](ALTER-TABLE-修改表.md) 与 [Online DDL](Online-DDL.md)。

## 12. 相关主题

- [DataType 数据类型](DataType-数据类型.md)
- [整数类型](整数类型.md)
- [定点与位类型](定点与位类型.md)
- [字符串与二进制类型](字符串.md)
- [日期类型](日期.md)
- [JSON 类型](JSON-类型.md)
- [空间数据类型](空间数据类型.md)
- [CREATE TABLE 创建表](CREATE-TABLE-创建表.md)

## 13. 参考资料

- [MySQL 8.4 Data Types](https://dev.mysql.com/doc/refman/8.4/en/data-types.html)
- [MySQL 8.4 CREATE TABLE Statement](https://dev.mysql.com/doc/refman/8.4/en/create-table.html)
- [MySQL 8.4 Data Type Default Values](https://dev.mysql.com/doc/refman/8.4/en/data-type-defaults.html)
- [MySQL 8.4 CREATE TABLE Generated Columns](https://dev.mysql.com/doc/refman/8.4/en/create-table-generated-columns.html)
- [MySQL 8.4 Invisible Columns](https://dev.mysql.com/doc/refman/8.4/en/invisible-columns.html)
