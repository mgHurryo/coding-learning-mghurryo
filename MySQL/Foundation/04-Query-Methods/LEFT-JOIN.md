---
title: LEFT JOIN
description: 讲解左外连接保留左表行、补 NULL、ON 与 WHERE 差异、反连接和正确计数。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# LEFT JOIN

## 方法定位

`LEFT JOIN`（左外连接）保留左表全部行。右表匹配时组合对应行，不匹配时右表列补 `NULL`。

## 基本语法

```sql

SELECT
    u.id,
    u.username,
    a.id AS article_id,
    a.title
FROM user AS u
LEFT JOIN article AS a
    ON a.user_id = u.id;
```

一名用户有多篇文章时仍会产生多行；没有文章的用户产生一行，文章列为 NULL。

## ON 与 WHERE 的关键差异

保留所有用户，只连接已发布文章：

```sql

SELECT u.id, a.id AS article_id
FROM user AS u
LEFT JOIN article AS a
  ON a.user_id = u.id
 AND a.status = 1;
```

错误地把右表条件放 `WHERE`：

```sql

SELECT u.id, a.id AS article_id
FROM user AS u
LEFT JOIN article AS a
  ON a.user_id = u.id
WHERE a.status = 1;
```

未匹配行的 `a.status` 是 NULL，会被 WHERE 删除，结果接近内连接。限制“哪些右表行参与匹配”的条件通常放在 `ON`。

## 查找没有关联记录的行

```sql

SELECT u.id, u.username
FROM user AS u
LEFT JOIN article AS a
    ON a.user_id = u.id
WHERE a.id IS NULL;
```

这称为反连接：找出没有任何文章的用户。也可使用 `NOT EXISTS`，语义通常更直接。

判断未匹配时选择右表的非空键（通常主键），不要选择本身允许 NULL 的普通列。

## 正确统计零关联数量

```sql

SELECT
    u.id,
    u.username,
    COUNT(a.id) AS article_count
FROM user AS u
LEFT JOIN article AS a
  ON a.user_id = u.id
 AND a.status = 1
GROUP BY u.id, u.username;
```

使用 `COUNT(a.id)` 而不是 `COUNT(*)`，这样补 NULL 的行得到 0。

## 过滤左表

左表条件放 `WHERE` 不会破坏“保留符合条件的左表行”：

```sql

SELECT u.id, a.id AS article_id
FROM user AS u
LEFT JOIN article AS a
    ON a.user_id = u.id
WHERE u.status = 1;
```

## 多个左连接的放大

同时连接两个一对多关系时，右表行可能交叉组合。例如 3 篇文章和 4 条其他关联可能产生 12 行。统计前应分别预聚合，或拆分查询。

## 性能基础

- 右表连接列应有合适索引，例如 `article.user_id`。
- 先确认左表过滤范围，避免保留大量无关行。
- 外连接的语义限制优化器可进行的重排。
- 使用 `EXPLAIN ANALYZE` 检查实际膨胀行数。

## 常见错误

- 把右表过滤条件放 `WHERE`，意外变成内连接效果。
- 用允许 NULL 的列判断是否未匹配。
- 用 `COUNT(*)` 统计右表数量。
- 认为左连接每个左表只返回一行。
- 多个一对多连接后直接聚合。

## 相关主题

- [JOIN 连接查询](JOIN-连接查询.md)
- [INNER JOIN](INNER-JOIN.md)
- [EXISTS 存在性查询](EXISTS-存在性查询.md)
- [IS NULL 空值判断](IS-NULL-空值判断.md)
