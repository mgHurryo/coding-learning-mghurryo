---
title: RIGHT JOIN
description: 讲解右外连接的保留语义、与 LEFT JOIN 的等价改写及团队可读性建议。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# RIGHT JOIN

## 方法定位

`RIGHT JOIN`（右外连接）保留右表全部行。左表不匹配时，左表列补 `NULL`。

## 基本语法

```sql

SELECT
    a.id AS article_id,
    c.id AS category_id,
    c.name AS category_name
FROM article AS a
RIGHT JOIN category AS c
    ON c.id = a.category_id;
```

所有分类都会保留，没有文章的分类对应的 `article_id` 为 NULL。

## 改写为 LEFT JOIN

交换表的左右位置即可改写：

```sql

SELECT
    a.id AS article_id,
    c.id AS category_id,
    c.name AS category_name
FROM category AS c
LEFT JOIN article AS a
    ON a.category_id = c.id;
```

两者表达相同的保留关系。多数团队统一使用 `LEFT JOIN`，让“主表 / 必须保留的表”写在左侧，长查询通常更容易从左到右阅读。

## ON 与 WHERE

和左连接一样，若在 `WHERE` 中要求左表列满足非 NULL 条件，会删除未匹配的补 NULL 行：

```sql

-- 会过滤没有已发布文章的分类
SELECT c.id, a.id
FROM article AS a
RIGHT JOIN category AS c
  ON c.id = a.category_id
WHERE a.status = 1;
```

若目的是保留所有分类并只匹配已发布文章，应把 `a.status = 1` 放入 `ON`。

## 什么时候使用

- 阅读现有以右侧为主表的 SQL。
- 查询结构天然从右侧表展开且团队允许该风格。
- 临时分析中，保留右表能让改写更短。

新代码若没有特殊原因，优先改写为 `LEFT JOIN`，减少认知切换。

## 常见错误

- 把右连接理解成按右表驱动物理执行；这是结果保留语义，不是执行计划保证。
- 右表条件、左表条件位置混乱。
- 与多个左 / 右连接混用，难以判断哪一侧保留。
- 需要全外连接却误以为 RIGHT JOIN 会保留两侧全部行。

## 相关主题

- [JOIN 连接查询](JOIN-连接查询.md)
- [LEFT JOIN](LEFT-JOIN.md)
- [UNION 集合查询](UNION-集合查询.md)
