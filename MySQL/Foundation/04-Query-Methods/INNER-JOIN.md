---
title: INNER JOIN
description: 讲解内连接的匹配语义、连接条件、一对多重复行、多表连接与性能基础。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# INNER JOIN

## 方法定位

`INNER JOIN` 只返回左右两侧都满足 `ON` 条件的行。`INNER` 可以省略，但显式写出更便于初学者区分连接类型。

## 基本语法

```sql

SELECT
    a.id,
    a.title,
    u.username
FROM article AS a
INNER JOIN user AS u
    ON u.id = a.user_id;
```

没有对应用户的文章，以及没有文章的用户，都不会出现在结果中。

## ON 是匹配规则

```sql

SELECT a.id, a.title, u.username
FROM article AS a
INNER JOIN user AS u
  ON u.id = a.user_id
 AND u.status = 1
WHERE a.status = 1;
```

对内连接而言，许多条件放 `ON` 或 `WHERE` 能得到相同结果，优化器也可能改写。但仍建议：

- 表之间如何关联写在 `ON`。
- 最终业务过滤写在 `WHERE`。
- 这种区分在改为外连接时尤其重要。

## 一对多会产生多行

一名用户有 3 篇文章时，从用户连接文章会返回该用户 3 行。这不是意外重复，而是结果粒度为“每篇文章一行”。

只想判断用户是否有文章时，使用 `EXISTS` 更贴近语义：

```sql

SELECT u.id, u.username
FROM user AS u
WHERE EXISTS (
    SELECT 1
    FROM article AS a
    WHERE a.user_id = u.id
);
```

## 多表内连接

```sql

SELECT
    a.id,
    a.title,
    u.username,
    c.name AS category_name
FROM article AS a
INNER JOIN user AS u
    ON u.id = a.user_id
INNER JOIN category AS c
    ON c.id = a.category_id
WHERE a.status = 1;
```

每个 `JOIN` 紧跟自己的 `ON`，避免遗漏或错配连接条件。

## 旧式逗号连接

```sql

SELECT a.id, u.username
FROM article AS a, user AS u
WHERE a.user_id = u.id;
```

虽然能表达内连接，但连接条件和过滤条件混在 `WHERE` 中，容易漏写并形成笛卡尔积。推荐显式 `JOIN ... ON`。

## 性能基础

- 连接列类型应一致或兼容。
- 被查找一侧连接列通常需要索引。
- 一对多的膨胀倍数比“有几个 JOIN”更重要。
- 优化器可重排内连接顺序；书写顺序不是物理执行顺序。
- 用 `EXPLAIN` 检查访问类型、索引和估算行数。

## 常见错误

- 忘记 `ON` 或连接列写错。
- 对一对多结果直接使用 `DISTINCT` 掩盖粒度问题。
- 同名列没有表前缀。
- 连接列类型不同导致隐式转换。
- 需要保留无匹配行却误用内连接。

## 相关主题

- [JOIN 连接查询](JOIN-连接查询.md)
- [LEFT JOIN](LEFT-JOIN.md)
- [EXISTS 存在性查询](EXISTS-存在性查询.md)
- [JOIN 调优](JOIN-调优.md)
