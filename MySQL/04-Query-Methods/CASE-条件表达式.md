---
title: CASE 条件表达式
description: 使用 CASE 在查询结果、排序和聚合中实现条件分支。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# CASE 条件表达式

## 方法定位

`CASE` 是 SQL 条件表达式，根据条件返回不同的值。它不会控制 SQL 是否执行，而是在每一行或每个分组上计算结果。

## 两种语法

简单 CASE 适合将一个表达式与多个值比较：

```sql

SELECT
    id,
    CASE status
        WHEN 0 THEN 'draft'
        WHEN 1 THEN 'published'
        ELSE 'unknown'
    END AS status_name
FROM article;
```

搜索 CASE 可以写任意条件：

```sql

SELECT
    id,
    CASE
        WHEN view_count >= 10000 THEN 'hot'
        WHEN view_count >= 1000 THEN 'popular'
        ELSE 'normal'
    END AS popularity
FROM article;
```

条件从上到下判断，命中第一个 `WHEN` 后停止。顺序错误会让后面的分支永远无法命中。

## 条件聚合

`CASE` 与聚合函数结合，可在一次扫描中统计多个口径：

```sql

SELECT
    user_id,
    COUNT(*) AS total_count,
    SUM(CASE WHEN status = 1 THEN 1 ELSE 0 END) AS published_count,
    SUM(CASE WHEN status = 0 THEN 1 ELSE 0 END) AS draft_count
FROM article
GROUP BY user_id;
```

也可以使用 `COUNT(CASE WHEN ... THEN 1 END)`。因为 `COUNT(expr)` 忽略 `NULL`，未命中的行不计数。

## 自定义排序

```sql

SELECT id, title, status
FROM article
ORDER BY
    CASE status
        WHEN 1 THEN 1
        WHEN 0 THEN 2
        ELSE 3
    END,
    create_time DESC;
```

适合业务优先级不是自然字典序的场景。复杂表达式排序可能无法直接使用普通索引，应关注数据规模。

## NULL 与返回类型

- 没有分支命中且省略 `ELSE` 时，结果为 `NULL`。
- 各分支最好返回语义和类型一致的值。
- `CASE` 不能替代 `WHERE`：只需要过滤行时直接写过滤条件更清楚。

## 常见错误

- 把范围较宽的条件放前面，遮蔽后续分支。
- 忘记 `END`。
- 条件计数时把未命中值写成 `0` 并放进 `COUNT`，导致所有行都被计数。
- 在大结果集上用复杂 `CASE` 排序，却没有评估 filesort 成本。

## 相关主题

- [Aggregate 聚合函数](Aggregate-聚合函数.md)
- [COUNT 统计](COUNT-统计.md)
- [ORDER BY 排序](ORDER-BY-排序.md)
