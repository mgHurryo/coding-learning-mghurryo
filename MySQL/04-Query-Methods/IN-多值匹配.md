---
title: IN 多值匹配
description: 讲解 IN 与 NOT IN 的候选列表、子查询、NULL 陷阱、参数绑定和与 EXISTS 的选择。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# IN 多值匹配

## 方法定位

`IN` 判断一个值是否等于候选集合中的任意值，是多个等值 `OR` 的紧凑写法。

```sql

SELECT id, title
FROM article
WHERE user_id IN (1, 2, 3);
```

等价语义：

```sql

WHERE user_id = 1
   OR user_id = 2
   OR user_id = 3
```

## IN 子查询

```sql

SELECT id, username
FROM user
WHERE id IN (
    SELECT user_id
    FROM article
    WHERE status = 1
);
```

这表示查询至少拥有一篇已发布文章的用户。也可使用 [EXISTS 存在性查询](EXISTS-存在性查询.md) 表达；现代优化器可能将二者改写为相似计划。

## NOT IN 与 NULL

```sql

SELECT id
FROM user
WHERE id NOT IN (1, 2, NULL);
```

这可能不返回任何行，因为“是否不等于 NULL”是未知。子查询列可能包含 NULL 时，优先写 `NOT EXISTS`，或在子查询中显式排除：

```sql

WHERE id NOT IN (
    SELECT user_id
    FROM article
    WHERE user_id IS NOT NULL
)
```

即使外层 `id` 非空，候选集合里的 NULL 仍会影响 `NOT IN`。

## 空列表问题

`IN ()` 是无效 SQL。应用生成动态条件时，应在集合为空时明确业务语义：

- “匹配空集合”应直接返回空结果或生成恒假条件。
- “没有筛选条件”则不应生成 `IN` 子句。

不要靠拼接字符串临时修补，应由查询构造层处理。

## 大列表与批量参数

候选值很少时 `IN` 清晰高效；列表达到数百、数千甚至更多时，要评估：

- SQL 文本和网络包大小。
- 解析成本与计划稳定性。
- 应用框架参数数量限制。
- 是否更适合临时表、批量表或持久化关系表后再连接。

值仍应使用参数绑定，而不是拼接用户输入。

## 索引与性能

`IN` 对同一列做多个等值匹配，列上合适的索引通常有帮助。但列表大小、选择性和返回比例会影响优化器选择。不要相信固定阈值，使用 `EXPLAIN` 观察。

## 常见错误

- `NOT IN` 忽略 NULL。
- 动态列表为空时生成 `IN ()`。
- 将用户输入直接拼进候选列表。
- 用超大 `IN` 承载本应建模为表关系的数据。
- 认为 `IN` 或 `EXISTS` 在所有场景中固定更快。

## 相关主题

- [EXISTS 存在性查询](EXISTS-存在性查询.md)
- [Subquery 子查询](Subquery-子查询.md)
- [WHERE 条件过滤](WHERE-条件过滤.md)
