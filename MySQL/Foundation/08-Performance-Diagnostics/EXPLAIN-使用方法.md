---
title: EXPLAIN 使用方法
description: 系统说明 MySQL 8.4 LTS 传统 EXPLAIN 的 12 个输出字段, 并用可执行的 users/orders 示例演示全表扫描、联合索引、覆盖索引、排序和连接计划。
tags:
  - MySQL
  - Performance
  - EXPLAIN
category: MySQL
mysql_version: "8.4 LTS"
---

# EXPLAIN 使用方法

> 本文以 MySQL 8.4 LTS 为基线. 默认讨论 `EXPLAIN FORMAT=TRADITIONAL` 的表格输出.

## 速览

`EXPLAIN` 用来查看优化器准备怎样执行 SQL. 它给出查询块、表访问顺序、访问方式、候选索引、实际索引、估算扫描量和额外操作. 阅读时不要只问"有没有用索引", 而要连续判断:

1. 优化器准备先访问哪个查询块和哪张表.
2. 每张表通过什么访问方式找到候选行.
3. 联合索引实际使用到哪一段, 用什么值与索引比较.
4. 预计读取多少行, 其中多少比例能通过本表条件.
5. 是否出现额外排序、临时表、回表、索引下推或连接缓冲.
6. 估算是否需要用 `EXPLAIN ANALYZE` 的实际数据验证.

`EXPLAIN` 的 `type` 是表访问方式, 不是字段的数据类型. `rows`、`filtered` 等也是成本模型的估算, 不是实际执行计数.

## 可执行实验环境

下面的脚本只应在本地实验 schema 中执行. 前两条 `DROP TABLE` 会删除 `explain_lab` 中的同名实验表.

```sql
CREATE DATABASE IF NOT EXISTS explain_lab
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_0900_ai_ci;

USE explain_lab;

DROP TABLE IF EXISTS orders;
DROP TABLE IF EXISTS users;

CREATE TABLE users (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    email VARCHAR(128) NOT NULL,
    display_name VARCHAR(64) NOT NULL,
    status TINYINT UNSIGNED NOT NULL DEFAULT 1,
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uk_users_email (email),
    KEY idx_users_status_created (status, created_at, id)
) ENGINE = InnoDB;

CREATE TABLE orders (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    user_id BIGINT UNSIGNED NOT NULL,
    order_no VARCHAR(32) NOT NULL,
    status TINYINT UNSIGNED NOT NULL DEFAULT 0,
    amount DECIMAL(10, 2) NOT NULL,
    created_at DATETIME NOT NULL,
    PRIMARY KEY (id),
    UNIQUE KEY uk_orders_order_no (order_no),
    KEY idx_orders_user_status_created (user_id, status, created_at, id),
    CONSTRAINT fk_orders_user
      FOREIGN KEY (user_id) REFERENCES users (id)
) ENGINE = InnoDB;

INSERT INTO users (id, email, display_name, status, created_at) VALUES
    (1, 'alice@example.com', 'Alice', 1, '2026-06-01 09:00:00'),
    (2, 'bob@example.com', 'Bob', 1, '2026-06-03 10:00:00'),
    (3, 'li.lei@example.com', 'Li Lei', 1, '2026-06-05 11:00:00'),
    (4, 'disabled@example.com', 'Disabled User', 0, '2026-06-07 12:00:00');

INSERT INTO orders
    (id, user_id, order_no, status, amount, created_at)
VALUES
    (1, 1, 'O20260701001', 1,  99.00, '2026-07-01 10:00:00'),
    (2, 1, 'O20260702001', 1, 199.00, '2026-07-02 11:00:00'),
    (3, 1, 'O20260703001', 0,  49.00, '2026-07-03 12:00:00'),
    (4, 1, 'O20260704001', 1, 299.00, '2026-07-04 13:00:00'),
    (5, 2, 'O20260701002', 1,  88.00, '2026-07-01 14:00:00'),
    (6, 2, 'O20260702002', 0, 188.00, '2026-07-02 15:00:00'),
    (7, 2, 'O20260703002', 1, 288.00, '2026-07-03 16:00:00'),
    (8, 3, 'O20260701003', 1,  77.00, '2026-07-01 17:00:00'),
    (9, 3, 'O20260702003', 1, 177.00, '2026-07-02 18:00:00'),
    (10, 4, 'O20260701004', 1, 66.00, '2026-07-01 19:00:00');

ANALYZE TABLE users, orders;
```

数据很少时, 成本模型可能认为全表扫描比走索引更便宜. 这是正常现象. 示例会说明如何观察计划, 但不应把某个实验环境中的 `rows` 或 `key_len` 数字当成固定答案.

## 12 个输出字段总览

| 字段 | 作用 | 主要判断方法 | 常见值或形态 |
| :--- | :--- | :--- | :--- |
| `id` | 标识 `SELECT` 查询块 | 区分外层查询、子查询、派生表和联合查询, 不机械等同于真实执行顺序 | 正整数, `NULL` |
| `select_type` | 描述查询块的类型 | 先判断是否为简单查询, 再识别子查询、派生表、物化或 `UNION` | `SIMPLE`、`PRIMARY`、`SUBQUERY`、`DERIVED`、`UNION`、`MATERIALIZED` 等 |
| `table` | 表示当前计划行正在访问的表或中间结果 | 看真实表别名, 也看 `<derivedN>`、`<unionM,N>`、`<subqueryN>` | 表名、别名、尖括号形式的内部结果 |
| `partitions` | 列出实际准备访问的分区 | 非分区表通常为 `NULL`; 分区表应检查是否发生分区裁剪 | `NULL` 或逗号分隔的分区名 |
| `type` | 表访问方式 | 识别等值定位、范围扫描、全索引扫描或全表扫描; 必须结合行数和成本看 | `const`、`eq_ref`、`ref`、`range`、`index`、`ALL` 等 |
| `possible_keys` | 优化器识别出的候选索引 | 有候选不代表会使用; `NULL` 也不代表表上完全没有索引 | 索引名列表或 `NULL` |
| `key` | 实际选择的索引 | 与预期索引比较, 再结合 `key_len`、`rows` 和 `Extra` 判断效果 | `PRIMARY`、索引名、`NULL` |
| `key_len` | 表示本次索引查找可能使用的最大键长度, 单位为字节 | 用于推断联合索引参与查找的前缀, 不能单独证明覆盖、过滤效果或实际存储长度 | 整数字节数或 `NULL` |
| `ref` | 表示拿什么值与索引键比较 | 判断索引查找来自常量、前表字段、表达式还是无比较值 | `const`、`db.table.column`、`func`、`NULL` |
| `rows` | 估算要从当前表读取的行数 | 看数量级和表间乘积, 与实际行数差异大时检查统计信息 | 非负估算整数 |
| `filtered` | 估算通过当前表条件的百分比 | 与 `rows` 联合估算传给下一步的行数 | `0.00` 到 `100.00` |
| `Extra` | 展示无法由前述字段完整表达的执行信息 | 重点识别覆盖索引、索引下推、额外过滤、排序、临时表和连接策略 | `Using index`、`Using where`、`Using filesort` 等 |

## id 与 select_type

### id

`id` 是传统输出中 `SELECT` 查询块的标识. 简单查询通常只有一个正整数. 子查询、派生表或 `UNION` 会产生多个查询块. 相同 `id` 的多行通常属于同一个查询块.

常见判断:

- 较大的 `id` 经常对应更内层的查询块, 但不能据此断言真实运行时一定先执行.
- MySQL 8.4 优化器可能合并派生表、物化子查询、改变连接顺序或消除查询块.
- 某些汇总结果, 例如 `UNION RESULT`, 可能显示 `id = NULL`.
- 真正的迭代执行结构应结合 `EXPLAIN FORMAT=TREE` 或 `EXPLAIN ANALYZE` 判断.

详见 [EXPLAIN id 字段](EXPLAIN-id-字段.md).

### select_type

`select_type` 说明当前计划行所属查询块的逻辑类型.

| 常见值 | 含义 | 判断重点 |
| :--- | :--- | :--- |
| `SIMPLE` | 不含 `UNION` 或子查询的简单查询 | 仍可能包含多表连接 |
| `PRIMARY` | 最外层查询块 | 不代表它一定先访问某张表 |
| `UNION` | `UNION` 中第二个及后续查询块 | 检查各分支扫描成本 |
| `DEPENDENT UNION` | 依赖外层行的 `UNION` 分支 | 可能被重复执行 |
| `UNION RESULT` | 汇总 `UNION` 结果的内部行 | `table` 常显示 `<union...>` |
| `SUBQUERY` | 子查询中的首个 `SELECT` | 查看是否被改写或只执行一次 |
| `DEPENDENT SUBQUERY` | 依赖外层行的相关子查询 | 重点关注外层行数乘以重复执行成本 |
| `DERIVED` | `FROM` 中的派生表 | 关注合并还是物化 |
| `DEPENDENT DERIVED` | 依赖其他表的派生表 | 可能随外层行重复计算 |
| `MATERIALIZED` | 被物化的子查询 | 关注物化数据量和后续查找 |
| `UNCACHEABLE SUBQUERY` | 结果不能复用的子查询 | 可能因变量或非确定性表达式重复执行 |
| `UNCACHEABLE UNION` | 不能复用的 `UNION` 分支 | 结合外层调用次数判断 |

`DEPENDENT` 或 `UNCACHEABLE` 是排查信号, 不是单独的性能结论. 还要看数据量、循环次数和优化器改写结果.

详见 [EXPLAIN select_type 字段](EXPLAIN-select_type-字段.md).

## table 与 partitions

### table

`table` 是当前计划行访问的对象. 对真实表通常显示 SQL 中的表名或别名. 对优化器生成的中间结果, 常见形态包括:

- `<derivedN>`: 来自 `id = N` 的派生表结果.
- `<unionM,N>`: 合并查询块 M 到 N 的 `UNION` 结果.
- `<subqueryN>`: 查询块 N 物化后的子查询结果.

阅读连接计划时, 传统输出中的行顺序通常反映优化器选择的访问顺序. 但复杂执行器细节仍以 TREE 或 ANALYZE 输出为准. 使用清晰表别名可以让 `table`、`ref` 和 SQL 条件更容易对应.

### partitions

`partitions` 列出将被访问的分区. 本文实验表没有分区, 因此通常显示 `NULL`.

对分区表应判断:

- 查询条件是否包含分区键.
- 输出是否只包含预期分区.
- 如果列出全部分区, 是否因为表达式、隐式转换或条件形态阻止了分区裁剪.
- 分区裁剪不能替代分区内的合理索引.

## type: 表访问方式

`type` 在这里表示 access type 或 join type, 不是 `INT`、`VARCHAR` 等字段数据类型. 它描述 MySQL 如何从当前表找到候选行.

下面按通常由精确到宽泛的方向排列. 这只是经验顺序, 不是脱离数据量和总成本的绝对排名.

| 常见值 | 含义 | 判断重点 |
| :--- | :--- | :--- |
| `system` | 表只有一行, 是特殊的 `const` | 很少见 |
| `const` | 通过主键或唯一索引常量等值条件, 最多匹配一行 | 通常很好, 但仍看后续连接 |
| `eq_ref` | 对前表每一行, 通过主键或唯一非空索引最多匹配一行 | 常见于主键连接 |
| `ref` | 通过非唯一索引或联合索引前缀做等值查找 | 看每个键值平均匹配多少行 |
| `fulltext` | 使用全文索引 | 结合全文条件和结果规模判断 |
| `ref_or_null` | 类似 `ref`, 同时额外查找 `NULL` | 常见于 `col = ? OR col IS NULL` |
| `index_merge` | 合并多个索引的扫描结果 | 不一定优于一个合适的联合索引 |
| `unique_subquery` | 用唯一索引查找替代某些 `IN` 子查询 | 查看子查询是否仍有较大外层成本 |
| `index_subquery` | 用普通索引查找替代某些 `IN` 子查询 | 可能返回多行 |
| `range` | 扫描索引的一个或多个范围 | 关注范围宽度、`rows` 和范围后的索引列能否继续过滤 |
| `index` | 扫描整棵索引 | 不是"高效命中索引"; 大索引全扫描仍可能很贵 |
| `ALL` | 扫描整张表 | 大表核心查询的高风险信号, 小表上可能是合理成本选择 |
| `NULL` | 不需要访问表或访问已被优化掉 | 结合 `Extra` 理解 |

`type` 较好不等于 SQL 一定快. 例如外层返回 100 万行时, 内层即使是 `eq_ref` 也可能执行 100 万次. 相反, 只有几行的小表使用 `ALL` 可能比随机索引读取更便宜.

详见 [EXPLAIN type 字段](EXPLAIN-type-字段.md).

## possible_keys、key、key_len 与 ref

### possible_keys

`possible_keys` 是优化器根据连接条件和过滤条件识别出的候选索引.

- `NULL` 表示没有识别到适合当前访问的候选索引, 不表示表上不存在任何索引.
- 出现多个索引不表示它们都会使用.
- 候选索引最终可能因为选择性差、回表成本高、表太小或统计信息而被放弃.
- 某些覆盖索引全扫描可能出现在 `key` 中, 即使它不在 `possible_keys` 中.

### key

`key` 是优化器最终选择的索引. `PRIMARY` 表示主键索引, 其他值通常是显式索引名, `NULL` 表示没有选择索引.

判断时至少同时查看:

1. `key` 是否符合查询条件和排序需求.
2. `key_len` 显示联合索引的查找前缀使用到哪里.
3. `rows` 是否真的比全表规模小.
4. `Extra` 是否出现 `Using index`、`Using index condition`、`Using filesort` 或 `Using temporary`.
5. 真实执行是否与估算一致.

详见 [EXPLAIN key 字段](EXPLAIN-key-字段.md).

### key_len

`key_len` 是优化器计划用于索引查找的最大键长度, 单位为字节. 它常用于推断联合索引有多少前导列参与了定位, 但有明确边界:

- 它是最大可能长度, 不是某一行索引值的实际存储字节数.
- 它主要描述用于查找的键部分, 不等于索引用于覆盖查询或排序的全部列.
- 可空列通常需要额外的 NULL 标记字节.
- 字符串长度受字符集最大字节数、变长长度字节和前缀索引影响.
- 表达式索引、排序规则、类型和版本都可能影响显示结果.
- 同一个索引名出现在 `key` 中, 不代表联合索引的每一列都参与了定位.
- 不应只靠背诵字节数判断计划. 应结合谓词、索引定义、`ref`、`Extra` 和 JSON/TREE 输出.

在本实验中, `idx_orders_user_status_created` 的前三列依次为 `BIGINT UNSIGNED`、`TINYINT UNSIGNED` 和 `DATETIME`, 且都为 `NOT NULL`. 等值使用前两列时, `key_len` 通常比再加入第三列范围条件时短. 具体值以当前实例输出为准.

### ref

`ref` 表示拿什么与 `key` 所示索引键进行比较.

| 常见值 | 含义 |
| :--- | :--- |
| `const` | 与 SQL 中的常量或已被常量化的值比较 |
| `schema.table.column` | 与前面已读取表的某个字段比较 |
| `func` | 与函数或表达式结果比较, 也可能提示隐式转换 |
| `NULL` | 当前访问没有这类索引等值比较 |

连接查询中, `ref` 可以帮助确认是否用预期的外键列连接. 如果看到 `func`, 应检查类型是否一致、是否发生字符集转换、是否对列进行了函数计算.

## rows 与 filtered

`rows` 是优化器估算从当前表需要读取的行数. InnoDB 的统计信息和数据分布决定这个估算, 因此它不是实际扫描行数.

`filtered` 是优化器估算读取的候选行中, 有多少百分比能通过当前表剩余条件. 对单个计划行, 可以粗略计算:

```text
预计传给下一步的行数 = rows * filtered / 100
```

例如:

```text
rows = 10000
filtered = 5.00
预计通过当前表条件的行数 = 10000 * 5.00 / 100 = 500
```

使用这个公式时要注意:

- 它是估算, 不是最终结果集的精确 `COUNT(*)`.
- 多表连接要沿访问顺序逐步考虑前表输出和后表每次查找, 不能只看某一行.
- 条件相关性、数据倾斜和过期统计信息会让估算偏差很大.
- 普通 `EXPLAIN` 不提供真实循环次数. 用 `EXPLAIN ANALYZE` 对比 estimated rows、actual rows 和 loops.
- 估算明显失真时, 先核对真实参数和数据分布, 再考虑 `ANALYZE TABLE`、直方图或索引调整.

详见 [EXPLAIN rows 字段](EXPLAIN-rows-字段.md).

## Extra

`Extra` 汇总额外执行信息. 一项计划中可能同时出现多个值.

| 常见值 | 含义 | 判断方法 |
| :--- | :--- | :--- |
| `Using index` | 仅从索引即可取得本步骤所需字段, 即覆盖索引 | 通常减少回表, 但仍要看扫描行数 |
| `Using index condition` | 使用 Index Condition Pushdown 在存储引擎层过滤索引记录 | 可以减少回表, 不等同于覆盖索引 |
| `Using where` | 读取候选行后仍需应用 WHERE 条件 | 很常见, 本身不是错误 |
| `Using filesort` | 不能直接按所需顺序从索引产出, 需要额外排序 | 不一定写磁盘; 关注参与排序的行数和 LIMIT |
| `Using temporary` | 需要内部临时表 | 常见于部分 GROUP BY、DISTINCT、UNION 或复杂派生结果 |
| `Using join buffer (hash join)` | 连接使用连接缓冲或哈希连接策略 | 检查连接条件、两侧索引和输入规模 |
| `Using MRR` | 使用 Multi-Range Read 批量读取行 | 可能降低二级索引回表的随机 I/O |
| `Using index for group-by` | 使用索引完成特定分组或去重优化 | 通常是积极信号 |
| `Range checked for each record` | 无固定理想索引, 对前表每行重新评估范围索引 | 大结果集上可能很贵 |
| `Not exists` | 优化器可在找到匹配后提前停止特定外连接查找 | 常见于反连接优化 |
| `Impossible WHERE` | 优化器判断条件不可能成立 | 检查参数或矛盾条件 |
| `Select tables optimized away` | 聚合等结果可由元数据或索引快速得到 | 表访问被简化 |

`Using filesort` 不等于一定发生磁盘文件排序, `Using temporary` 也不等于一定落盘. 它们是需要结合数据量、内存、行宽和实际耗时继续判断的信号.

详见 [EXPLAIN Extra 字段](EXPLAIN-Extra-字段.md).

## 示例 1: 无可用索引的全表扫描

`display_name` 没有索引, 且前导通配符不能做普通 B+Tree 前缀定位.

```sql
EXPLAIN
SELECT id, email, display_name
FROM users
WHERE display_name LIKE '%li%';
```

重点观察:

- `select_type` 通常为 `SIMPLE`.
- `table` 为 `users`.
- `type` 通常为 `ALL`.
- `possible_keys` 和 `key` 通常为 `NULL`.
- `Extra` 常包含 `Using where`.
- 小表的 `ALL` 不一定需要优化; 大表高频接口则应重新设计检索方式.

## 示例 2: 联合索引的等值加范围查询

查询条件按 `idx_orders_user_status_created(user_id, status, created_at, id)` 的最左前缀排列.

```sql
EXPLAIN
SELECT id, order_no, amount, created_at
FROM orders
WHERE user_id = 1
  AND status = 1
  AND created_at >= '2026-07-01 00:00:00'
ORDER BY created_at DESC
LIMIT 10;
```

重点观察:

- `possible_keys` 应包含 `idx_orders_user_status_created`.
- 在数据量足够、有利于索引时, `key` 可选择该联合索引, `type` 常见为 `range`.
- `key_len` 可反映前两列等值和第三列范围是否参与索引定位.
- 查询还读取 `order_no` 和 `amount`, 这两个字段不在该索引中, 因此通常不是覆盖索引.
- 前两列为等值条件后, 索引中的 `created_at` 可以继续支持时间范围和排序; 是否出现 `Using filesort` 以实际计划为准.

实验数据太少时, 优化器可能选择 `ALL`. 仅为了观察索引结构可以在实验环境比较下面的计划, 但生产 SQL 不应把 `FORCE INDEX` 当作默认修复:

```sql
EXPLAIN
SELECT id, order_no, amount, created_at
FROM orders FORCE INDEX (idx_orders_user_status_created)
WHERE user_id = 1
  AND status = 1
  AND created_at >= '2026-07-01 00:00:00'
ORDER BY created_at DESC
LIMIT 10;
```

## 示例 3: 覆盖索引

所选字段都可以从联合索引取得. InnoDB 二级索引叶子节点还包含主键值.

```sql
EXPLAIN
SELECT id, user_id, status, created_at
FROM orders FORCE INDEX (idx_orders_user_status_created)
WHERE user_id = 1
  AND status = 1
ORDER BY created_at DESC
LIMIT 10;
```

重点观察:

- `key` 为 `idx_orders_user_status_created`.
- `type` 常见为 `ref`.
- `ref` 常显示两个 `const` 比较.
- `Extra` 常包含 `Using index`, 表示覆盖索引.
- `key_len` 主要反映用于查找的 `user_id` 和 `status`, 不能因为它未计入全部索引列就判断索引没有用于排序或覆盖.

## 示例 4: 无匹配排序索引

`status` 和 `amount` 没有按查询所需顺序组成索引.

```sql
EXPLAIN
SELECT id, order_no, amount
FROM orders
WHERE status = 1
ORDER BY amount DESC;
```

重点观察:

- 当前索引不能以 `status` 作为最左列定位, 因此可能出现 `type = ALL`.
- `Extra` 常同时包含 `Using where` 和 `Using filesort`.
- 不要只因看到 `Using filesort` 就立即建 `amount` 单列索引. 应结合过滤条件、返回规模、分页方式和接口频率设计联合索引.

## 示例 5: 连接中的 const、eq_ref/ref 和 ref

```sql
EXPLAIN
SELECT u.email, o.id, o.amount
FROM users AS u
JOIN orders AS o FORCE INDEX (idx_orders_user_status_created)
  ON o.user_id = u.id
WHERE u.email = 'alice@example.com'
  AND o.status = 1;
```

重点观察:

- `users` 可通过唯一索引 `uk_users_email` 形成 `const` 访问.
- `orders` 可通过联合索引前缀按用户和状态查找, 常见 `type = ref`.
- `ref` 可能显示 `const` 或前表字段, 具体取决于优化器是否先把唯一用户行常量化.
- 优化器可根据成本改变连接顺序. 不要把 SQL 书写顺序当作执行顺序.
- 对更一般的主键连接, 被连接表按主键为前表每行匹配至多一行时, 常见 `eq_ref`.

连接调优详见 [JOIN 调优](JOIN-调优.md).

## 一套可复用的阅读顺序

面对真实慢 SQL, 按下面的顺序记录证据:

1. 还原 Mapper SQL、真实绑定参数、表结构、索引定义和数据量.
2. 看 `id`、`select_type`、`table`, 确认查询块与访问顺序.
3. 看 `partitions`, 确认分区裁剪.
4. 看 `type`, 区分精确查找、范围扫描、全索引扫描和全表扫描.
5. 对比 `possible_keys` 与 `key`, 判断候选索引为什么被采用或放弃.
6. 结合 `key_len` 与 `ref`, 判断联合索引查找前缀和比较来源.
7. 用 `rows * filtered / 100` 估算每一步输出量, 找到成本放大的位置.
8. 看 `Extra` 中的回表、索引下推、排序、临时表和连接策略.
9. 用 `EXPLAIN ANALYZE` 验证估算与实际差异, 再做索引或 SQL 改写.
10. 修改后保存前后计划、实际耗时和结果一致性证据.

## EXPLAIN ANALYZE 的执行与安全边界

普通 `EXPLAIN` 主要展示估算计划. `EXPLAIN ANALYZE` 会真正执行语句, 并在 TREE 输出中显示实际耗时、实际行数和循环次数.

```sql
EXPLAIN ANALYZE
SELECT id, user_id, status, created_at
FROM orders
WHERE user_id = 1
  AND status = 1
ORDER BY created_at DESC
LIMIT 10;
```

安全要求:

- 只对已经确认只读、结果规模和耗时可控的查询使用.
- 不要把 `EXPLAIN ANALYZE` 当成"只看计划". 它会消耗 CPU、I/O、Buffer Pool 和连接资源.
- 不要在生产高峰直接分析未知慢 SQL. 优先在接近生产数据分布的测试环境或受控只读副本验证.
- 避免带 `FOR UPDATE`、`FOR SHARE`、长事务、用户自定义函数或其他潜在副作用的语句.
- 对写语句保持更严格边界. 即使当前语法支持某类 DML, 也不要在生产数据上用 ANALYZE 试探.
- 必要时设置会话级执行时间上限并监控连接, 但超时不能替代容量评估和变更审批.
- 估算和实际差异大时, 检查统计信息、参数分布和数据倾斜, 不要只靠强制索引掩盖问题.

详见 [EXPLAIN ANALYZE](EXPLAIN-ANALYZE.md) 和 [optimizer trace](optimizer-trace.md).

## 常见错误

- 把 `type` 误认为字段数据类型.
- 只要 `key` 非空就认定 SQL 已经足够快.
- 把 `index` 访问误读成精准索引查找, 忽略它可能扫描整棵索引.
- 把 `possible_keys = NULL` 误读成表上没有任何索引.
- 用 `key_len` 的字节数直接推断覆盖索引, 或死记固定字节数.
- 把 `rows` 当成实际扫描行数, 忽略 `filtered` 和循环次数.
- 看到 `Using filesort` 或 `Using temporary` 就机械加索引.
- 忽略小表、缓存命中、行宽、回表和结果集传输成本.
- 在生产环境对未知语句直接运行 `EXPLAIN ANALYZE`.
- 优化后只看执行计划, 不校验结果正确性和实际延迟.

## 相关主题

### EXPLAIN 字段

- [EXPLAIN id 字段](EXPLAIN-id-字段.md)
- [EXPLAIN select_type 字段](EXPLAIN-select_type-字段.md)
- [EXPLAIN type 字段](EXPLAIN-type-字段.md)
- [EXPLAIN key 字段](EXPLAIN-key-字段.md)
- [EXPLAIN rows 字段](EXPLAIN-rows-字段.md)
- [EXPLAIN Extra 字段](EXPLAIN-Extra-字段.md)

### 诊断与调优

- [EXPLAIN ANALYZE](EXPLAIN-ANALYZE.md)
- [optimizer trace](optimizer-trace.md)
- [慢 SQL 排查流程](慢-SQL-排查流程.md)
- [WHERE 调优](WHERE-调优.md)
- [JOIN 调优](JOIN-调优.md)
- [ORDER BY 调优](ORDER-BY-调优.md)
- [GROUP BY 调优](GROUP-BY-调优.md)

### 索引基础

- [Index 索引概念](Index-索引概念.md)
- [Composite Index 联合索引](Composite-Index-联合索引.md)
- [Covering Index 覆盖索引](Covering-Index-覆盖索引.md)
