---
title: JOIN 连接查询
description: 系统讲解 MySQL 多表连接的匹配模型、连接类型、基数、ON 与 WHERE 差异、重复行和性能要点。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# JOIN 连接查询

## 连接在做什么

`JOIN` 按条件将两侧表的行组合成更宽的结果行。理解连接时不要只背语法，应先回答三个问题：

1. 左侧每一行能匹配右侧几行？
2. 没有匹配的行是否需要保留？
3. 过滤条件属于“匹配规则”还是“最终结果过滤”？

## 基本语法

```sql

SELECT
    a.id,
    a.title,
    u.username
FROM article AS a
INNER JOIN user AS u
    ON u.id = a.user_id
WHERE a.status = 1;
```

- `article AS a` 和 `user AS u` 是表别名。
- `ON u.id = a.user_id` 是连接条件。
- `WHERE a.status = 1` 是连接后的行过滤。
- 多表存在同名列时必须用表别名限定，否则会出现歧义。

## 连接类型速查

| 类型 | 返回结果 |
| :--- | :--- |
| [INNER JOIN](INNER-JOIN.md) | 只保留左右两侧匹配成功的组合 |
| [LEFT JOIN](LEFT-JOIN.md) | 保留左侧全部行；右侧不匹配的列补 NULL |
| [RIGHT JOIN](RIGHT-JOIN.md) | 保留右侧全部行；左侧不匹配的列补 NULL |
| `CROSS JOIN` | 返回笛卡尔积，行数约为左右行数乘积 |
| 自连接 | 同一张表以不同别名连接，适合层级或行间比较 |

MySQL 没有原生 `FULL OUTER JOIN`。确有需要时，可用左连接与反向未匹配结果通过 `UNION ALL` 组合。

## 连接基数决定结果行数

假设一个用户有 3 篇文章：

- 一对一：用户连接档案，通常一名用户产生一行。
- 一对多：用户连接文章，一名用户可能产生多行。
- 多对多：文章连接标签，中间表会让一篇文章产生多行。

连接后“重复”往往不是数据库出错，而是业务关系本来就是一对多。不要先用 `DISTINCT` 掩盖，应检查连接条件和期望粒度。

## ON 与 WHERE：外连接最常见陷阱

### 条件写在 ON 中

```sql

SELECT u.id, u.username, a.id AS article_id
FROM user AS u
LEFT JOIN article AS a
  ON a.user_id = u.id
 AND a.status = 1;
```

返回所有用户；没有已发布文章的用户仍会出现，文章列为 `NULL`。

### 条件写在 WHERE 中

```sql

SELECT u.id, u.username, a.id AS article_id
FROM user AS u
LEFT JOIN article AS a
  ON a.user_id = u.id
WHERE a.status = 1;
```

未匹配行的 `a.status` 为 `NULL`，不满足条件，因此被删除。这个查询的结果接近内连接。

判断方法：限制右表“哪些行可以参与匹配”的条件通常放 `ON`；限制最终结果的条件放 `WHERE`。

## 一对多连接后的聚合

统计每个用户的已发布文章数，同时保留零篇用户：

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

这里必须用 `COUNT(a.id)` 而不是 `COUNT(*)`。左表用户即使没有文章，也会形成一行补 NULL 的连接结果；`COUNT(*)` 会计为 1，而 `COUNT(a.id)` 忽略 NULL，正确得到 0。

## 自连接

假设分类表包含 `parent_id`，可以将表与自身连接：

```sql

SELECT
    child.name AS child_name,
    parent.name AS parent_name
FROM category AS child
LEFT JOIN category AS parent
    ON parent.id = child.parent_id;
```

两个别名代表同一张表在查询中的不同角色。

## CROSS JOIN 与笛卡尔积

```sql

SELECT u.id, c.id
FROM user AS u
CROSS JOIN category AS c;
```

若有 1000 名用户和 100 个分类，结果是 100000 行。笛卡尔积在生成组合时有用，但遗漏 `ON` 导致的意外笛卡尔积会迅速放大数据量。

## 多表连接的可读性规范

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
WHERE a.status = 1
ORDER BY a.create_time DESC, a.id DESC;
```

- 每个 `JOIN` 紧跟自己的 `ON`。
- 使用短但有含义的别名。
- 输出列逐行书写，避免 `SELECT *`。
- 所有连接列明确表前缀。
- 连接条件与业务过滤条件分开。

## 性能基础

- 被连接的键应具有相同或兼容的数据类型和字符集 / 排序规则。
- 通常为被查找一侧的连接列建立索引，例如 `article.user_id`。
- 优化器可能重排内连接顺序，不要把 SQL 书写顺序当成物理驱动顺序。
- 连接前可过滤的数据尽量尽早过滤，但最终以执行计划为准。
- 关注实际行数、连接后膨胀倍数、回表和临时表。
- 使用 [EXPLAIN 使用方法](../08-Performance-Diagnostics/EXPLAIN-使用方法.md) 和 [EXPLAIN ANALYZE](../08-Performance-Diagnostics/EXPLAIN-ANALYZE.md) 验证。

## 常见错误

- 忘记或写错 `ON`，造成笛卡尔积。
- 把左连接右表过滤条件放在 `WHERE`，意外过滤未匹配行。
- 一对多连接后误以为结果“重复”，直接加 `DISTINCT`。
- 连接列类型不同，触发隐式转换。
- 同名列不加表别名。
- 为取某个关联值连接多行表，却没有定义应该取哪一行。
- 同时连接多个一对多关系后直接聚合，导致交叉放大计数。

## 练习

1. 查询所有用户及其已发布文章，没有文章的用户也要显示。
2. 统计每个分类的文章数，并保留零文章分类。
3. 找出没有发布任何文章的用户，分别用左连接和 `NOT EXISTS` 实现。
4. 解释为什么同时连接“文章-评论”和“文章-标签”后计数可能变大。

## 相关主题

- [SELECT 逻辑执行顺序](SELECT-逻辑执行顺序.md)
- [EXISTS 存在性查询](EXISTS-存在性查询.md)
- [JOIN 调优](../08-Performance-Diagnostics/JOIN-调优.md)
