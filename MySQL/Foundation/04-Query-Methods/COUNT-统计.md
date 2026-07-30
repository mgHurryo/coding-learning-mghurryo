---
title: COUNT 统计
description: 区分 COUNT(*)、COUNT(column)、COUNT(DISTINCT) 和外连接计数，解释 NULL 与性能误区。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# COUNT 统计

## 三种常见写法

| 写法 | 统计内容 |
| :--- | :--- |
| `COUNT(*)` | 结果集中的所有行 |
| `COUNT(column)` | column 非 NULL 的行 |
| `COUNT(DISTINCT column)` | column 非 NULL 的不同值数量 |

```sql

SELECT
    COUNT(*) AS total_rows,
    COUNT(category_id) AS rows_with_category,
    COUNT(DISTINCT user_id) AS author_count
FROM article;
```

## COUNT(*) 与 COUNT(1)

在现代 MySQL / InnoDB 中，两者通常会被优化为等价的行数统计。`COUNT(*)` 语义最清楚：统计行。不要用 `COUNT(1)` 作为固定更快的口诀。

`COUNT(primary_key)` 也会忽略 NULL，但主键本身非空。它仍不如 `COUNT(*)` 直接表达“统计所有行”。

## 条件计数

```sql

SELECT
    user_id,
    COUNT(*) AS total_count,
    COUNT(CASE WHEN status = 1 THEN 1 END) AS published_count
FROM article
GROUP BY user_id;
```

未命中的 CASE 返回 NULL，因此 `COUNT` 不计数。若写 `ELSE 0`，0 仍是非 NULL，会导致所有行都被统计。

也可以写：

```sql

SUM(CASE WHEN status = 1 THEN 1 ELSE 0 END)
```

## 左连接计数陷阱

统计每个用户的文章数并保留零文章用户：

```sql

SELECT
    u.id,
    COUNT(a.id) AS article_count
FROM user AS u
LEFT JOIN article AS a
    ON a.user_id = u.id
GROUP BY u.id;
```

不能写 `COUNT(*)`：即使没有文章，左连接仍为用户生成一行右侧补 NULL 的记录，`COUNT(*)` 会得到 1；`COUNT(a.id)` 忽略 NULL，得到 0。

## 连接放大

若文章同时连接评论和标签，一篇文章的评论行与标签行可能交叉组合，使 `COUNT(*)` 或 `COUNT(article.id)` 放大。解决方式不是机械加 `DISTINCT`，而是先明确粒度，并分别预聚合或拆分统计。

## COUNT(DISTINCT)

```sql

SELECT category_id, COUNT(DISTINCT user_id) AS author_count
FROM article
WHERE status = 1
GROUP BY category_id;
```

去重计数适合“每个分类有多少不同作者”，通常比普通计数成本高。MySQL 也支持多个表达式的不同组合计数，使用前应确认版本语法与 NULL 规则。

## InnoDB 为什么不能瞬间精确 COUNT

InnoDB 需要考虑事务可见性，不维护一个对所有事务都准确的简单总行数。因此无条件 `COUNT(*)` 在大表上仍可能扫描索引。近似统计、缓存计数和汇总表都涉及一致性权衡。

## 常见错误

- 把 `COUNT(column)` 当作总行数。
- 认为 `COUNT(1)` 永远更快。
- 左连接使用 `COUNT(*)` 导致零变一。
- 多个一对多连接导致重复计数。
- 需要精确实时总数，却低估大表成本。

## 相关主题

- [Aggregate 聚合函数](Aggregate-聚合函数.md)
- [JOIN 连接查询](JOIN-连接查询.md)
- [GROUP BY 分组](GROUP-BY-分组.md)
