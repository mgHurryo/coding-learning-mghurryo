---
title: DQL MOC
description: 面向初学者的 MySQL 8.4 DQL 学习入口，覆盖查询结构、执行顺序、过滤、聚合、连接、子查询、CTE、窗口函数与性能基础。
tags:
  - MySQL
  - DQL
  - MOC
category: MySQL
---

# DQL MOC

> DQL（Data Query Language，数据查询语言）用于读取数据，核心语句是 `SELECT`。本栏目以 MySQL 8.4 LTS 为边界，既讲“怎么写”，也解释“为什么这样写”和“什么时候不该这样写”。

## 初学者先记住

1. 查询不会天然按主键或插入顺序返回；需要稳定顺序时必须写 `ORDER BY`。
2. SQL 中的 `NULL` 表示“未知或缺失”，不能用 `= NULL` 判断。
3. `WHERE` 过滤原始行，`HAVING` 过滤分组后的结果。
4. `JOIN` 的 `ON` 决定表之间如何匹配，`WHERE` 决定匹配后保留哪些行。
5. `LIMIT` 只限制返回数量，不等于查询一定高效。
6. 生产代码避免 `SELECT *`，应明确列名并检查执行计划。
7. SQL 关键字不区分大小写，但推荐大写；字符串使用单引号。

## 从一条完整查询认识 DQL

```sql

SELECT
    a.category_id,
    COUNT(*) AS article_count,
    MAX(a.create_time) AS latest_time
FROM article AS a
WHERE a.status = 1
  AND a.create_time >= '2026-01-01'
GROUP BY a.category_id
HAVING COUNT(*) >= 10
ORDER BY article_count DESC, a.category_id ASC
LIMIT 20;
```

这条查询依次表达：从文章表读取已发布且在指定日期之后的数据，按分类分组，保留文章数不少于 10 的分类，再按文章数降序返回前 20 条。

## 语法书写顺序

```sql

WITH cte_name AS (...)
SELECT [DISTINCT] select_list
FROM table_source
[JOIN table_source ON join_condition]
[WHERE row_condition]
[GROUP BY grouping_columns]
[HAVING group_condition]
[WINDOW window_definition]
[ORDER BY sort_expression]
[LIMIT row_count [OFFSET offset]];
```

方括号表示可选部分，不是 SQL 的实际字符。详细执行逻辑见 [SELECT 逻辑执行顺序](SELECT-逻辑执行顺序.md)。

## 学习地图

### 第一阶段：读懂单表查询

| 主题 | 解决的问题 |
| :--- | :--- |
| [SELECT 基础查询](SELECT-基础查询.md) | 选择列、别名、常量、去重 |
| [运算符与表达式](运算符与表达式.md) | 比较、算术、逻辑和优先级 |
| [WHERE 条件过滤](WHERE-条件过滤.md) | 从原始数据中筛选行 |
| [LIKE 模糊查询](LIKE-模糊查询.md) | 按字符模式匹配 |
| [IN 多值匹配](IN-多值匹配.md) | 匹配一组候选值 |
| [BETWEEN 范围查询](BETWEEN-范围查询.md) | 闭区间筛选与时间边界 |
| [IS NULL 空值判断](IS-NULL-空值判断.md) | 正确处理未知值 |
| [CASE 条件表达式](CASE-条件表达式.md) | 在查询结果中分支判断 |
| [常用查询函数](常用查询函数.md) | 字符串、数值、日期和 NULL 函数 |
| [ORDER BY 排序](ORDER-BY-排序.md) | 生成可预测的结果顺序 |
| [LIMIT 分页](LIMIT-分页.md) | 限制行数和实现分页 |

### 第二阶段：汇总数据

| 主题 | 解决的问题 |
| :--- | :--- |
| [Aggregate 聚合函数](Aggregate-聚合函数.md) | 求和、平均、极值和计数 |
| [COUNT 统计](COUNT-统计.md) | 区分 `COUNT(*)`、列计数和去重计数 |
| [GROUP BY 分组](GROUP-BY-分组.md) | 将明细行按维度归组 |
| [HAVING 分组后过滤](HAVING-分组后过滤.md) | 按聚合结果筛选分组 |

### 第三阶段：组合多表和多结果集

| 主题 | 解决的问题 |
| :--- | :--- |
| [JOIN 连接查询](JOIN-连接查询.md) | 理解连接模型、基数和 `ON` / `WHERE` |
| [INNER JOIN](INNER-JOIN.md) | 只保留两侧都匹配的行 |
| [LEFT JOIN](LEFT-JOIN.md) | 保留左表全部行 |
| [RIGHT JOIN](RIGHT-JOIN.md) | 保留右表全部行 |
| [UNION 集合查询](UNION-集合查询.md) | 纵向合并多个查询结果 |

### 第四阶段：复杂分析

| 主题 | 解决的问题 |
| :--- | :--- |
| [Subquery 子查询](Subquery-子查询.md) | 在查询内部嵌套查询 |
| [EXISTS 存在性查询](EXISTS-存在性查询.md) | 判断关联记录是否存在 |
| [CTE 公用表表达式](CTE-公用表表达式.md) | 为复杂查询命名并进行递归查询 |
| [Window Functions 窗口函数](Window-Functions-窗口函数.md) | 排名、累计、同比和组内 Top N |

## 贯穿示例数据模型

本栏目示例默认使用以下三张简化表：

```sql

CREATE TABLE user (
    id BIGINT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    status TINYINT NOT NULL,
    create_time DATETIME NOT NULL
);

CREATE TABLE category (
    id BIGINT PRIMARY KEY,
    name VARCHAR(50) NOT NULL
);

CREATE TABLE article (
    id BIGINT PRIMARY KEY,
    user_id BIGINT NOT NULL,
    category_id BIGINT NOT NULL,
    title VARCHAR(200) NOT NULL,
    status TINYINT NOT NULL,
    view_count INT NOT NULL DEFAULT 0,
    create_time DATETIME NOT NULL,
    INDEX idx_article_user_status (user_id, status),
    INDEX idx_article_category_time (category_id, create_time)
);
```

示例不会依赖外键约束，但默认 `article.user_id` 对应 `user.id`，`article.category_id` 对应 `category.id`。

## 一条查询的自检清单

- 结果列是否只包含调用方需要的数据？
- 表别名、列别名是否清楚，连接查询中的列是否带表前缀？
- `NULL`、空字符串、零值是否被正确区分？
- 多个 `AND` / `OR` 是否用括号明确分组？
- 外连接条件是否错误地放入 `WHERE`？
- 分页是否包含唯一且稳定的排序字段？
- 聚合查询的非聚合列是否都符合分组规则？
- 用户输入是否通过参数绑定，而不是字符串拼接？
- 大表查询是否检查过 [EXPLAIN 使用方法](EXPLAIN-使用方法.md)？
- 是否为高频过滤、连接和排序路径设计了合适索引？

## 常见问题

### DQL 和 DML 是什么关系

一些教材把 `SELECT` 单独归为 DQL，也有资料把它视为广义 DML 的一部分。分类不同不影响语法；本知识库将读取操作称为 DQL，将写入操作归入 DML。

### 为什么相同 SQL 的行顺序可能变化

关系表没有默认顺序。执行计划、索引、缓存状态或数据分布变化后，MySQL 可以选择不同访问路径。只有 `ORDER BY` 才定义输出顺序。

### 为什么查询正确但很慢

语法正确只说明能得到结果，不说明扫描量合理。常见原因包括缺少索引、返回列过多、深分页、连接基数爆炸、不可索引条件、排序或分组使用临时表。先用 `EXPLAIN` 定位，不靠猜测加索引。

## 推荐练习顺序

1. 写单表查询并组合 `WHERE`、`ORDER BY`、`LIMIT`。
2. 统计每个用户的文章数，比较 `WHERE` 与 `HAVING`。
3. 用内连接和左连接查询作者及文章，并解释未匹配行。
4. 分别用 `IN`、`EXISTS` 和连接解决“发布过文章的用户”。
5. 用窗口函数完成“每个分类阅读量最高的 3 篇文章”。
6. 对以上 SQL 执行 `EXPLAIN`，观察访问类型、索引和估算行数。

## 版本边界与参考

- 主线：MySQL 8.4 LTS。
- CTE 和窗口函数要求 MySQL 8.0+。
- MySQL 不支持原生 `FULL OUTER JOIN`，通常通过 `UNION ALL` 组合左右两侧结果。
- 官方手册：[MySQL 8.4 SELECT Statement](https://dev.mysql.com/doc/refman/8.4/en/select.html)
