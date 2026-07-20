---
title: EXISTS 存在性查询
description: 使用 EXISTS 与 NOT EXISTS 判断关联行是否存在，并比较 IN、JOIN 和 NULL 陷阱。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# EXISTS 存在性查询

## 方法定位

`EXISTS (subquery)` 只关心子查询是否至少返回一行，不关心具体返回值。适合表达“存在关联记录”和“不存在关联记录”。

## 查找存在关联记录的行

查询至少发布过一篇文章的用户：

```sql

SELECT u.id, u.username
FROM user AS u
WHERE EXISTS (
    SELECT 1
    FROM article AS a
    WHERE a.user_id = u.id
      AND a.status = 1
);
```

子查询引用外层的 `u.id`，因此属于关联子查询。`SELECT 1` 表示返回内容不重要；写 `SELECT *` 通常语义较差，但优化器往往不会真的读取所有列。

## 查找不存在关联记录的行

```sql

SELECT u.id, u.username
FROM user AS u
WHERE NOT EXISTS (
    SELECT 1
    FROM article AS a
    WHERE a.user_id = u.id
      AND a.status = 1
);
```

这是“反连接”语义：找出没有已发布文章的用户。

## 与 JOIN 的区别

```sql

SELECT u.id, u.username
FROM user AS u
INNER JOIN article AS a
    ON a.user_id = u.id
   AND a.status = 1;
```

若一名用户有多篇文章，`JOIN` 会返回多行用户；`EXISTS` 只判断是否存在，外层每名用户仍是一行。若最终不需要右表列，`EXISTS` 往往更直接。

## 与 IN 的区别

非关联的 `IN` 也能表达类似语义：

```sql

SELECT id, username
FROM user
WHERE id IN (
    SELECT user_id
    FROM article
    WHERE status = 1
);
```

现代 MySQL 优化器可能把 `IN` 和 `EXISTS` 改写为相近计划，应根据语义和 `EXPLAIN` 选择，不要依赖“EXISTS 永远更快”这类口诀。

## NOT IN 的 NULL 陷阱

```sql

SELECT id
FROM user
WHERE id NOT IN (1, 2, NULL);
```

由于与 `NULL` 的比较是未知，这个条件可能不返回任何行。子查询列可能含 NULL 时，优先使用 `NOT EXISTS`，或明确在子查询中过滤 NULL。

## 索引建议

关联条件 `a.user_id = u.id` 是高频查找路径，右表应有以 `user_id` 开头的索引。若同时固定 `status`，联合索引顺序应结合整体查询模式与选择性评估。

## 常见错误

- 在 `EXISTS` 子查询中返回大量无关表达式，降低可读性。
- 用 `JOIN + DISTINCT` 只是为了判断存在性。
- 使用 `NOT IN` 却没有考虑子查询中的 NULL。
- 认为关联子查询一定逐行完整执行；优化器可能进行半连接等改写，应看执行计划。

## 相关主题

- [Subquery 子查询](Subquery-子查询.md)
- [IN 多值匹配](IN-多值匹配.md)
- [JOIN 连接查询](JOIN-连接查询.md)
