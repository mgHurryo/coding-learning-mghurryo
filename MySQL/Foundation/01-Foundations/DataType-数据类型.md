---
title: DataType 数据类型
description: 以 MySQL 8.4 LTS 为基线，系统说明全部字段类型、选型依据、列属性及可执行建模示例。
tags:
  - MySQL
  - Basics
  - Data-Type
  - Schema-Design
category: MySQL
version: MySQL-8.4-LTS
---

# DataType 数据类型

> 本文是 MySQL 字段类型的总入口。类型不是“能否存进去”的语法选择，而是数据域、精度、比较规则、索引成本和应用映射之间的契约。

## 1. 科学选型模型

定义字段前依次回答以下问题：

1. **值域**：最小值、最大值和未来增长上限是什么？
2. **精度**：必须十进制精确，还是允许测量误差？
3. **语义**：它是数量、金额、文本、字节、时间点、时长、枚举、文档还是空间对象？
4. **比较规则**：是否区分大小写、重音、尾随空格？是否需要按数值或时间排序？
5. **访问方式**：会做等值查询、范围查询、聚合、排序、全文检索还是空间检索？
6. **应用边界**：Java、JavaScript 或其他客户端能否无损表示该范围和精度？

优先选择“能够覆盖明确未来上限的最小准确类型”，而不是机械追求最小字节数。类型过小会溢出，类型过大则会放大数据页、二级索引、Buffer Pool 和网络传输成本。

## 2. MySQL 8.4 字段类型全景

### 2.1 数值类型

| 类型 | 核心作用 | 典型字段 | 关键边界 |
| :--- | :--- | :--- | :--- |
| `TINYINT` | 1 Byte 小整数 | 状态、等级、布尔标记 | 有符号 -128 到 127；`UNSIGNED` 0 到 255 |
| `SMALLINT` | 2 Byte 小范围整数 | 年龄上限、端口、短计数 | 有符号 -32768 到 32767 |
| `MEDIUMINT` | 3 Byte 中等范围整数 | 中等规模编号 | 使用较少；有符号约正负 838 万 |
| `INT` / `INTEGER` | 4 Byte 常规整数 | 数量、计数、普通编号 | 有符号约正负 21 亿 |
| `BIGINT` | 8 Byte 大整数 | 主键、订单号、大计数 | Java `long` 只能覆盖有符号范围 |
| `DECIMAL(M,D)` / `NUMERIC` | 精确十进制定点数 | 金额、税率、结算数量 | `M <= 65`、`D <= 30`，`M` 含整数位和小数位 |
| `FLOAT` | 4 Byte 近似数 | 低精度传感器值 | 约 7 位十进制有效数字，不能保证十进制等值 |
| `DOUBLE` / `REAL` | 8 Byte 近似数 | 科学计算、坐标外的连续测量 | 约 15 位十进制有效数字；`REAL` 受 SQL mode 影响 |
| `BIT(M)` | 1 到 64 个位 | 权限位、质量标志 | 客户端常按二进制字节返回，不等同布尔 |
| `BOOL` / `BOOLEAN` | `TINYINT(1)` 的别名 | 真假标记 | 不是独立布尔类型，仍可写入 2，需 `CHECK` 约束 |

详见：[整数类型](整数类型.md)、[浮点类型](浮点类型.md)、[定点与位类型](定点与位类型.md)。

常见兼容别名包括：`DEC`、`FIXED` 等价于 `DECIMAL`；`DOUBLE PRECISION` 等价于 `DOUBLE`；`SERIAL` 等价于 `BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE`。新表优先写出真实类型和约束，不依赖隐式别名。

### 2.2 日期时间类型

| 类型 | 核心作用 | 典型字段 | 关键边界 |
| :--- | :--- | :--- | :--- |
| `DATE` | 只记录日历日期 | 生日、账单日、合同日期 | 不带时刻和时区 |
| `TIME(fsp)` | 一天内时间或时间间隔 | 工时、持续时间 | 可超过 24 小时，范围约正负 838 小时 |
| `DATETIME(fsp)` | 字面日期时间 | 预约、门店营业时间 | 不按会话时区自动转换，范围到 9999 年 |
| `TIMESTAMP(fsp)` | 跨时区事件时间点 | 创建、更新、审计时间 | 按会话时区转换，UTC 范围约 1970 到 2038 年 |
| `YEAR` | 年份 | 车型年款、统计年度 | 1901 到 2155，另允许 0000 |

`fsp` 是小数秒精度，范围 0 到 6。跨系统事件通常统一存 UTC；纯日历语义不要强行转换成时间戳。详见：[日期类型](日期.md)。

### 2.3 字符与二进制类型

| 类型 | 核心作用 | 典型字段 | 关键边界 |
| :--- | :--- | :--- | :--- |
| `CHAR(M)` | 固定字符数文本 | 国家码、固定格式代码 | `M` 是字符数，0 到 255；比较受 collation 影响 |
| `VARCHAR(M)` | 可变字符数文本 | 用户名、标题、邮箱 | 受 65535 Byte 行大小、字符集及其他列共同限制 |
| `TINYTEXT` / `TEXT` / `MEDIUMTEXT` / `LONGTEXT` | 4 级长文本 | 摘要、正文、大文档 | 上限约 255 B、64 KiB、16 MiB、4 GiB |
| `BINARY(M)` | 固定字节串 | SHA-256、二进制定长标识 | `M` 是字节数；短值以 `0x00` 补齐 |
| `VARBINARY(M)` | 可变字节串 | 密文、协议载荷 | 按字节比较，不经过字符集转换 |
| `TINYBLOB` / `BLOB` / `MEDIUMBLOB` / `LONGBLOB` | 4 级二进制大对象 | 图片、压缩包、原始载荷 | 上限与对应 TEXT 相同，实际还受包和内存限制 |
| `ENUM` | 单选封闭集合 | 长期稳定的小状态集 | 内部按声明序号存储和排序，演进通常需要 DDL |
| `SET` | 最多 64 个成员的位集合 | 极少变化的小型组合标志 | 不适合可增长标签或多对多关系 |

`NCHAR` / `NVARCHAR` 是国家字符集兼容写法，不应代替显式的 `CHARACTER SET` 和 `COLLATE` 设计。详见：[字符串与二进制类型](字符串.md)、[枚举与集合类型](枚举与集合类型.md)、[字符集与排序规则](Charset-字符集与排序规则.md)。

### 2.4 JSON 类型

| 类型 | 核心作用 | 适合 | 不适合 |
| :--- | :--- | :--- | :--- |
| `JSON` | 保存经过语法校验的二进制 JSON 文档 | 稀疏、可演进、读取模式相对稳定的扩展属性 | 主键、金额、状态等核心字段；需要强外键关系的数据 |

JSON 不是“免设计”的替代品。频繁过滤或排序的路径应提升为普通列、generated column 或合适的函数/多值索引。详见：[JSON 类型](JSON-类型.md)。

### 2.5 空间类型

| 类型 | 作用 | 例子 |
| :--- | :--- | :--- |
| `GEOMETRY` | 接受任意空间子类型 | 通用空间对象容器 |
| `POINT` | 单个点 | 门店、设备、经纬度 |
| `LINESTRING` | 有序点形成的线 | 道路、轨迹 |
| `POLYGON` | 闭合面及可选内环 | 行政区、配送区 |
| `MULTIPOINT` | 多个点 | 站点集合 |
| `MULTILINESTRING` | 多条线 | 多段路线 |
| `MULTIPOLYGON` | 多个面 | 飞地、多片服务区 |
| `GEOMETRYCOLLECTION` | 异构空间对象集合 | 混合几何结果 |

空间字段应明确 SRID、坐标轴顺序、几何有效性和查询谓词。需要空间检索时使用 `SPATIAL INDEX` 并用 `EXPLAIN` 验证。详见：[空间数据类型](空间数据类型.md)。

> MySQL 8.4 LTS 没有原生 `VECTOR` 字段。向量能力属于其他版本或产品边界，不能把新版本语法直接写进 8.4 schema。

## 3. 长度、精度和标度不是一回事

| 写法 | 参数含义 | 反例 |
| :--- | :--- | :--- |
| `VARCHAR(64)` | 最多 64 个字符，实际字节受字符集影响 | 不是固定 64 Byte |
| `VARBINARY(64)` | 最多 64 Byte | 不是 64 个 Unicode 字符 |
| `DECIMAL(12,2)` | 总共 12 位数字，其中 2 位小数 | 整数部分只能有 10 位 |
| `BIT(8)` | 8 个 bit | 不是数值范围 0 到 8 |
| `DATETIME(6)` | 6 位小数秒 | 不是显示宽度 |
| `INT(11)` | 遗留显示宽度语法 | 不改变 4 Byte 存储和 INT 范围 |

不要在 MySQL 8.4 新设计中使用 `ZEROFILL`、`INT(11)`、`FLOAT(M,D)` 或 `DOUBLE(M,D)` 表达业务格式。格式属于展示层；精度约束应使用正确类型和业务校验。

## 4. 可执行建模示例：账户表
~~~sql
CREATE DATABASE IF NOT EXISTS type_lab
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_0900_ai_ci;

USE type_lab;

CREATE TABLE customer_account (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT
        COMMENT '内部聚簇主键',
    public_id BINARY(16) NOT NULL
        COMMENT 'UUID 的 16 Byte 表示',
    username VARCHAR(64) NOT NULL
        COMMENT '唯一性按表级 ai_ci collation 判断',
    status TINYINT UNSIGNED NOT NULL DEFAULT 1
        COMMENT '0=disabled, 1=active, 2=locked',
    credit_limit DECIMAL(12,2) NOT NULL DEFAULT 0.00
        COMMENT '必须精确的十进制额度',
    risk_score DOUBLE NULL
        COMMENT '允许近似误差的模型分数',
    birth_date DATE NULL
        COMMENT '只含日期，不伪造时刻',
    preferences JSON NOT NULL DEFAULT (JSON_OBJECT())
        COMMENT '低频扩展属性',
    preference_locale VARCHAR(16)
        GENERATED ALWAYS AS (
          JSON_UNQUOTE(JSON_EXTRACT(preferences, '$.locale'))
        ) STORED,
    created_at TIMESTAMP(6) NOT NULL DEFAULT CURRENT_TIMESTAMP(6),
    updated_at TIMESTAMP(6) NOT NULL DEFAULT CURRENT_TIMESTAMP(6)
        ON UPDATE CURRENT_TIMESTAMP(6),
    PRIMARY KEY (id),
    UNIQUE KEY uk_customer_public_id (public_id),
    UNIQUE KEY uk_customer_username (username),
    KEY idx_customer_preference_locale (preference_locale),
    CHECK (status IN (0, 1, 2)),
    CHECK (credit_limit >= 0),
    CHECK (JSON_TYPE(preferences) = 'OBJECT')
) ENGINE=InnoDB;

INSERT INTO customer_account
    (public_id, username, status, credit_limit, risk_score, birth_date, preferences)
VALUES
    (
      UUID_TO_BIN(UUID(), TRUE),
      'Ada',
      1,
      15000.00,
      0.03125,
      '1995-12-10',
      JSON_OBJECT('locale', 'zh-CN', 'newsletter', TRUE)
    );

SELECT
    id,
    BIN_TO_UUID(public_id, TRUE) AS public_id,
    username,
    credit_limit,
    preference_locale,
    created_at
FROM customer_account
WHERE username = 'ADA';
~~~

表级 `utf8mb4_0900_ai_ci` 大小写和重音不敏感，因此 `username = 'ADA'` 可以匹配 `Ada`，对应的 UNIQUE 也按同一等价规则判重。若业务要求区分大小写，应显式选择 `_as_cs` 或 `_bin` collation，而不是额外套一个 `LOWER()` 就假设完成了 Unicode 规范化。

### 4.1 每个字段为什么这样设计

| 字段 | 类型与属性 | 作用 | 如果选错会怎样 |
| :--- | :--- | :--- | :--- |
| `id` | `BIGINT UNSIGNED AUTO_INCREMENT` | 小而稳定的聚簇索引键，支撑长期增长 | 随机长字符串主键会放大所有二级索引并增加页分裂 |
| `public_id` | `BINARY(16)` + UNIQUE | 对外 UUID，固定 16 Byte 且按字节比较 | `VARCHAR(36)` 占用更大，还引入字符集比较 |
| `username` | `VARCHAR(64)` + UNIQUE | 人类文本；相等和唯一性由 collation 定义 | 忽略 collation 会让大小写冲突规则与产品预期不一致 |
| `status` | `TINYINT UNSIGNED` + `CHECK` | 小型状态值，约束合法域 | 只有 `TINYINT(1)` 并不能阻止写入 2 |
| `credit_limit` | `DECIMAL(12,2)` | 精确金额运算和对账 | `DOUBLE` 可能产生二进制浮点误差 |
| `risk_score` | `DOUBLE` | 统计模型的近似连续量 | 用超大 `DECIMAL` 会增加转换成本且没有业务收益 |
| `birth_date` | `DATE` | 保存纯日历日期 | `TIMESTAMP` 会制造无意义时区和 2038 边界 |
| `preferences` | `JSON` + CHECK | 保存非核心、稀疏扩展属性 | 把状态或金额藏入 JSON 会削弱约束和索引 |
| `preference_locale` | STORED generated column + index | 将高频 JSON 路径提升为有界标量访问路径 | 每次动态提取路径会增加计算成本且没有普通索引入口 |
| `created_at` | `TIMESTAMP(6)` + DEFAULT | 记录创建事件时间点 | 应用漏传时没有默认值会产生空审计时间 |
| `updated_at` | `TIMESTAMP(6)` + `ON UPDATE` | 自动记录最近数据库行更新 | 不能记录操作者和字段差异，不等于完整审计日志 |

`BIGINT UNSIGNED` 最大值超过 Java `long`。如果应用不能保证 ID 不超过 `Long.MAX_VALUE`，应使用 `BigInteger`、无符号转换方案或直接保留有符号 `BIGINT`。

## 5. 可执行实验：精确值与近似值

~~~sql
CREATE TEMPORARY TABLE numeric_lab (
    exact_value DECIMAL(20,10) NOT NULL,
    approximate_value DOUBLE NOT NULL
);

INSERT INTO numeric_lab VALUES
    (0.1, 0.1),
    (0.2, 0.2);

SELECT
    SUM(exact_value) = CAST(0.3 AS DECIMAL(20,10)) AS decimal_equal,
    SUM(approximate_value) = 0.3 AS double_equal,
    ABS(SUM(approximate_value) - 0.3) AS double_error
FROM numeric_lab;
~~~

`DECIMAL` 用十进制定点规则参与计算，适合财务等值；`DOUBLE` 的结果应使用容差或业务定义的舍入规则比较，不要假设 `=` 永远可靠。传感器值本身已有测量误差时，`DOUBLE` 往往比伪装成“精确”的大 `DECIMAL` 更科学。

金额也可以用最小货币单位整数，例如将人民币分存为 `BIGINT`。这种方案只有在币种小数位固定、单位转换集中、溢出上限明确时才可靠；多币种和可变小数位结算通常使用 `DECIMAL` 更清晰。

## 6. 可执行实验：TIMESTAMP 与 DATETIME

~~~sql
CREATE TEMPORARY TABLE time_lab (
    event_ts TIMESTAMP NOT NULL,
    local_schedule DATETIME NOT NULL
);

SET time_zone = '+08:00';
INSERT INTO time_lab VALUES
    ('2026-07-20 10:00:00', '2026-07-20 10:00:00');

SET time_zone = '+00:00';
SELECT event_ts, local_schedule FROM time_lab;
~~~

会话时区改变后，`event_ts` 显示为同一时间点的 UTC 表示；`local_schedule` 仍是字面上的 10:00。航班起飞瞬间、日志时间适合时间点语义；门店“每天 10:00 开门”或某地预约更接近日历语义。

时间范围查询使用半开区间，避免遗漏小数秒：

~~~sql
SELECT id, created_at
FROM customer_account
WHERE created_at >= '2026-07-20 00:00:00'
  AND created_at <  '2026-07-21 00:00:00';
~~~

## 7. 字段属性的作用
字段完整语法不只有数据类型：

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
    [COMMENT 'text']
~~~

非字面量默认表达式通常需要括号，并受允许函数和列依赖顺序限制；`CURRENT_TIMESTAMP[(fsp)]` 对 `TIMESTAMP` / `DATETIME` 有专门语法。`ON UPDATE` 也只用于这两类时间字段的 `CURRENT_TIMESTAMP[(fsp)]` 自动更新，不是任意表达式钩子。

| 属性 | 作用 | 设计要点 |
| :--- | :--- | :--- |
| `NULL` / `NOT NULL` | 是否允许“未知或不适用” | 空字符串、0 和 JSON `null` 都不是 SQL `NULL` |
| `DEFAULT` | INSERT 省略列或使用 `DEFAULT` 时的值 | UPDATE 也可 `SET col = DEFAULT`；显式 `NULL` 不等于默认 |
| `UNSIGNED` | 去掉整数负区间并扩大正上限 | 会影响运算、外键类型匹配和语言映射 |
| `AUTO_INCREMENT` | 由 MySQL 分配递增整数 | 一表通常只有一个，且必须被索引 |
| `CHARACTER SET` / `COLLATE` | 决定编码、相等和排序 | 唯一索引的“唯一”也受 collation 影响 |
| `GENERATED ... VIRTUAL` | 从表达式派生列值 | 未索引值通常不在聚簇行保存；索引化后仍占二级索引并增加写成本 |
| `GENERATED ... STORED` | 写入时计算并保存在聚簇行 | 占行空间，适合读取频繁或需要稳定物化的表达式 |
| `ON UPDATE CURRENT_TIMESTAMP` | 时间列在行更新时自动刷新 | 仅适用于 `TIMESTAMP` / `DATETIME`，不等于完整业务审计 |
| `INVISIBLE` | 默认不出现在 `SELECT *` | 迁移兼容工具；显式列名仍可访问 |
| `COMMENT` | 给 schema 自描述信息 | 不应只写“状态”，应写单位、取值和语义 |
| `SRID` | 限制空间引用系统 | 空间索引和距离计算必须与 SRID 一致 |

主键、唯一、外键和 CHECK 是约束，不只是注释。CHECK 只拒绝 FALSE，UNKNOWN 会通过；需要必填输入时还应使用 `NOT NULL`。详见：[Column 字段](Column-字段.md)、[PRIMARY KEY](PRIMARY-KEY-主键约束.md)、[UNIQUE](UNIQUE-唯一约束.md)、[FOREIGN KEY](FOREIGN-KEY-外键约束.md)、[NOT NULL](NOT-NULL-非空约束.md)、[DEFAULT](DEFAULT-默认值.md)。

## 8. 类型和索引的共同设计

1. **比较两侧类型保持一致**：不要用字符串参数查询整数列，避免隐式转换和错误结果。
2. **索引列尽量短而稳定**：二级索引叶子还保存主键值，因此长主键会放大每个二级索引。
3. **长文本按访问路径拆分**：列表查询不要读取正文或 BLOB；必要时拆到扩展表。
4. **JSON 路径显式索引**：只有查询表达式和索引定义匹配，优化器才可能利用它。
5. **空间数据使用空间语义**：经纬度拆成两个 `DOUBLE` 可以存值，但不能替代 SRID、空间谓词和空间索引。
6. **先约束再索引**：`CHECK`、`UNIQUE` 和外键保证数据质量；普通索引主要改善访问成本，两者职责不同。
7. **用 `EXPLAIN ANALYZE` 验证**：不要仅凭“字段更小”推断性能，必须观察真实行数、耗时和执行路径。

## 9. 常见反模式及修正

| 反模式 | 问题 | 修正 |
| :--- | :--- | :--- |
| 所有列都用 `VARCHAR(255)` | 丢失数值/时间语义，约束松散，索引变大 | 按值域和运算选择具体类型 |
| 金额用 `FLOAT` | 近似误差影响汇总和对账 | `DECIMAL` 或经过严格单位设计的整数 |
| 日期用字符串 | 非法日期、排序和范围查询复杂 | `DATE`、`DATETIME` 或 `TIMESTAMP` |
| `TINYINT(1)` 当强布尔 | 仍可保存非 0/1 | 加 `CHECK (col IN (0,1))` |
| 核心属性全部塞进 JSON | 缺少强类型、外键和直接索引 | 稳定高频属性提升为普通列 |
| 标签用逗号字符串或 `SET` | 难以约束、连接和统计 | 标签表与关联表；只在严格封闭集合使用 `SET` |
| UUID 存 `CHAR(36)` 作聚簇主键 | 宽、随机、放大二级索引 | 内部窄主键 + `BINARY(16)` 唯一业务标识 |
| 只看声明长度不看字符集 | utf8mb4 可能占多字节 | 按最坏字节数评估行和索引 |
| 用 `SELECT *` 读取 TEXT/BLOB | 网络、内存和反序列化成本不可控 | 显式列出列表页所需字段 |

## 10. 建表前检查清单

- [ ] 字段的单位、值域、空值、默认值和生命周期已定义。
- [ ] 金额、比率和测量值已区分精确与近似语义。
- [ ] 文本字符集和 collation 与大小写规则一致。
- [ ] 时间字段已区分日期、日历时间、时间点和时长。
- [ ] JSON、ENUM、SET 和空间类型确有适用边界，而非为了省建模。
- [ ] 主键宽度、二级索引放大和未来数据量已估算。
- [ ] 数据库类型能被应用语言和驱动无损映射。
- [ ] 约束能拒绝非法值，示例 INSERT 和边界值已测试。
- [ ] 常用查询已用 `EXPLAIN` / `EXPLAIN ANALYZE` 验证。
- [ ] schema 变更有迁移、兼容、回滚和校验方案。

## 11. 相关主题

- [Column 字段](Column-字段.md)
- [Charset 字符集与排序规则](Charset-字符集与排序规则.md)
- [CREATE TABLE 创建表](CREATE-TABLE-创建表.md)
- [ALTER TABLE 修改表](ALTER-TABLE-修改表.md)
- [Index 索引概念](Index-索引概念.md)
- [EXPLAIN 使用方法](EXPLAIN-使用方法.md)

## 12. 参考资料

- [MySQL 8.4 Numeric Data Types](https://dev.mysql.com/doc/refman/8.4/en/numeric-types.html)
- [MySQL 8.4 Date and Time Data Types](https://dev.mysql.com/doc/refman/8.4/en/date-and-time-types.html)
- [MySQL 8.4 String Data Types](https://dev.mysql.com/doc/refman/8.4/en/string-types.html)
- [MySQL 8.4 JSON Data Type](https://dev.mysql.com/doc/refman/8.4/en/json.html)
- [MySQL 8.4 Spatial Data Types](https://dev.mysql.com/doc/refman/8.4/en/spatial-types.html)
- [MySQL 8.4 Data Type Storage Requirements](https://dev.mysql.com/doc/refman/8.4/en/storage-requirements.html)
