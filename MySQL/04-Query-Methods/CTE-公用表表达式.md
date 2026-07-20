---
title: CTE 公用表表达式
description: 使用 WITH 为复杂查询命名，讲解非递归与递归 CTE、作用域、可读性和性能边界。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# CTE 公用表表达式

## 方法定位

CTE（Common Table Expression，公用表表达式）使用 `WITH` 为一个临时结果集命名，只在当前语句中有效。MySQL 8.0+ 支持 CTE。

它主要提升复杂查询的可读性和复用性，不等于创建临时表，也不保证一定物化或一定更快。

## 非递归 CTE

```sql

WITH published_article AS (
    SELECT id, user_id, category_id, view_count
    FROM article
    WHERE status = 1
)
SELECT
    user_id,
    COUNT(*) AS article_count,
    SUM(view_count) AS total_views
FROM published_article
GROUP BY user_id;
```

`published_article` 可像表一样被后续查询引用。复杂查询可以定义多个 CTE：

```sql

WITH
published_article AS (
    SELECT user_id, view_count
    FROM article
    WHERE status = 1
),
user_stats AS (
    SELECT user_id, COUNT(*) AS article_count
    FROM published_article
    GROUP BY user_id
)
SELECT u.username, s.article_count
FROM user_stats AS s
JOIN user AS u ON u.id = s.user_id;
```

## CTE 与派生表

派生表写在 `FROM (...) AS alias` 中：

```sql

SELECT x.user_id, x.article_count
FROM (
    SELECT user_id, COUNT(*) AS article_count
    FROM article
    GROUP BY user_id
) AS x
WHERE x.article_count >= 10;
```

CTE 能把命名结果放到语句顶部，嵌套层级较多或同一结果多次引用时通常更清楚。简单的一次性子查询无需为了“高级写法”强行改成 CTE。

## 递归 CTE

递归 CTE 由锚点查询和递归查询组成，并使用 `UNION ALL` 连接：

```sql

WITH RECURSIVE sequence_number AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1
    FROM sequence_number
    WHERE n < 10
)
SELECT n
FROM sequence_number;
```

执行思路：

1. 锚点生成初始行 `n = 1`。
2. 递归部分读取上一轮结果并生成 `n + 1`。
3. 当 `WHERE n < 10` 不再满足时结束。

递归 CTE 可用于树形目录、组织层级、连续序列和图遍历。必须有可靠终止条件；还要关注 MySQL 的递归深度限制。

## 树形查询示例

假设 `category(id, parent_id, name)`：

```sql

WITH RECURSIVE category_tree AS (
    SELECT id, parent_id, name, 0 AS depth
    FROM category
    WHERE id = 100

    UNION ALL

    SELECT c.id, c.parent_id, c.name, t.depth + 1
    FROM category AS c
    JOIN category_tree AS t
      ON c.parent_id = t.id
)
SELECT id, parent_id, name, depth
FROM category_tree
ORDER BY depth, id;
```

生产场景还需考虑环路检测、最大深度和路径展示。

## 性能边界

- CTE 是查询组织方式，不自动缓存为永久结果。
- 优化器可以合并或物化 CTE，具体行为应通过 `EXPLAIN` 验证。
- 多次引用同一大结果集不代表只计算一次。
- CTE 内也应尽早过滤不需要的行和列。
- 递归查询的中间结果可能快速增长，应限制深度和分支。

## 常见错误

- 忘记 MySQL 版本要求。
- 递归部分没有终止条件。
- 锚点和递归分支列数或类型不兼容。
- 误以为 CTE 必然提升性能。
- CTE 名称与真实业务粒度不符，反而降低可读性。

## 相关主题

- [Subquery 子查询](Subquery-子查询.md)
- [UNION 集合查询](UNION-集合查询.md)
- [EXPLAIN 使用方法](../08-Performance-Diagnostics/EXPLAIN-使用方法.md)
