---
title: SELECT 逻辑执行顺序
description: 区分 SQL 的书写顺序与逻辑处理顺序，解释别名可见性及 WHERE、HAVING、ORDER BY 的职责。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# SELECT 逻辑执行顺序

## 为什么要学习执行顺序

SQL 按 `SELECT ... FROM ... WHERE ...` 书写，但理解结果时应从数据来源开始。逻辑执行顺序可以解释：为什么 `WHERE` 通常不能使用 `SELECT` 别名，而 `ORDER BY` 可以。

> 逻辑顺序用于理解语义，不等于数据库引擎实际逐步执行的物理顺序。优化器可以在保持结果一致的前提下改写查询。

## 常用逻辑顺序
| 顺序  | 子句 / 阶段            | 作用              |
| :-: | :----------------- | :-------------- |
|  1  | `WITH`             | 构造 CTE，供主查询引用   |
|  2  | `FROM` / `JOIN`    | 确定数据源并组合行       |
|  3  | `ON`               | 判断连接匹配关系        |
|  4  | `WHERE`            | 过滤原始行           |
|  5  | `GROUP BY`         | 将剩余行划分为组        |
|  6  | `HAVING`           | 过滤分组            |
|  7  | 窗口函数               | 在分组结果上计算排名、累计等值 |
|  8  | `SELECT`           | 计算其他输出表达式和别名    |
|  9  | `DISTINCT`         | 去除重复结果行         |
| 10  | `ORDER BY`         | 对最终结果排序         |
| 11  | `LIMIT` / `OFFSET` | 截取需要返回的行        |

这是帮助初学者理解别名可见性和子句职责的逻辑模型，不是 MySQL 内部逐行执行算法。窗口函数在分组、`HAVING` 之后计算，并先于最终去重、排序和限制；优化器仍可在保持语义的前提下改写物理执行计划。

## 别名为什么有时不可用

```sql

SELECT view_count * 2 AS doubled_views
FROM article
WHERE doubled_views > 100;
```

这通常会报“未知列”，因为逻辑上 `WHERE` 先于 `SELECT`，此时别名尚未产生。可以重复表达式，或将查询包在派生表 / CTE 中：

```sql

WITH scored_article AS (
    SELECT id, view_count * 2 AS doubled_views
    FROM article
)
SELECT id, doubled_views
FROM scored_article
WHERE doubled_views > 100;
```

`ORDER BY` 位于 `SELECT` 之后，因此通常可以使用输出别名：

```sql

SELECT id, view_count * 2 AS doubled_views
FROM article
ORDER BY doubled_views DESC;
```

## WHERE 和 HAVING 的区别

```sql

SELECT category_id, COUNT(*) AS article_count
FROM article
WHERE status = 1
GROUP BY category_id
HAVING COUNT(*) >= 10;
```

- `WHERE status = 1`：分组前排除未发布文章，减少参与聚合的数据。
- `HAVING COUNT(*) >= 10`：分组后保留文章数达到条件的分类。
- 能在 `WHERE` 完成的原始行过滤不要无故推迟到 `HAVING`。

## 外连接中 ON 和 WHERE 的差异

```sql

-- 保留没有已发布文章的用户
SELECT u.id, a.id AS article_id
FROM user AS u
LEFT JOIN article AS a
  ON a.user_id = u.id
 AND a.status = 1;
```

若把 `a.status = 1` 放到 `WHERE`，未匹配行中的 `a.status` 为 `NULL`，会被过滤，查询效果接近内连接。详见 [JOIN 连接查询](JOIN-连接查询.md)。

## 执行顺序不等于性能顺序

优化器可能进行条件下推、连接重排、子查询改写、索引选择等操作。不要仅凭 SQL 的书写位置猜测性能，使用 [EXPLAIN 使用方法](../08-Performance-Diagnostics/EXPLAIN-使用方法.md) 检查实际计划。

## 相关主题

- [DQL MOC](DQL-MOC.md)
- [WHERE 条件过滤](WHERE-条件过滤.md)
- [HAVING 分组后过滤](HAVING-分组后过滤.md)
- [JOIN 连接查询](JOIN-连接查询.md)
