---
title: IS NULL 空值判断
description: 解释 SQL NULL 的未知语义、三值逻辑、IS NULL、NULL 安全比较、聚合和索引行为。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# IS NULL 空值判断

## NULL 是什么

`NULL` 表示值缺失、未知或不适用。它不是空字符串 `''`、数字 `0`、布尔假，也不是字符串 `'NULL'`。

## 正确判断方式

```sql

SELECT id
FROM article
WHERE category_id IS NULL;

SELECT id
FROM article
WHERE category_id IS NOT NULL;
```

不要写 `category_id = NULL` 或 `category_id <> NULL`，普通比较结果是未知。

## 三值逻辑

SQL 条件可能是真、假或未知：

```sql

SELECT
    NULL = NULL,
    NULL <> 1,
    NULL IS NULL,
    NULL <=> NULL;
```

MySQL 的 `<=>` 是 NULL 安全等于：

- 两侧都为 NULL：真。
- 只有一侧为 NULL：假。
- 都非 NULL：按普通相等比较。

## NOT 不会自动包含 NULL

```sql

WHERE NOT (category_id = 1)
```

这不会保留 `category_id IS NULL` 的行。若业务要求“不是 1 或未知”，应明确写：

```sql

WHERE category_id <> 1
   OR category_id IS NULL
```

## NULL 与聚合函数

- `COUNT(*)` 统计行数。
- `COUNT(column)` 统计该列非 NULL 的行数。
- `SUM`、`AVG`、`MIN`、`MAX` 通常忽略 NULL。
- 如果所有输入都是 NULL，许多聚合结果仍为 NULL。

```sql

SELECT
    COUNT(*) AS all_rows,
    COUNT(category_id) AS non_null_categories
FROM article;
```

## NULL 与排序

MySQL 升序排序时，NULL 通常排在非 NULL 之前；降序时通常在之后。需要明确控制时，可以先按布尔表达式排序：

```sql

-- 非 NULL 在前，NULL 在后
SELECT id, category_id
FROM article
ORDER BY category_id IS NULL, category_id ASC;
```

## 使用 COALESCE

```sql

SELECT id, COALESCE(category_id, 0) AS display_category_id
FROM article;
```

`COALESCE` 返回第一个非 NULL 值，适合展示或计算默认值，但不会修改原数据。不要用无业务含义的默认值混淆“未知”和真实的 0。

## NULL 与索引

MySQL B+Tree 索引可以包含 NULL，`IS NULL` 可能使用索引。是否值得使用取决于 NULL 比例、返回行数和其他条件，需通过 `EXPLAIN` 确认。

## 建模建议

- 业务必填字段优先使用 `NOT NULL`。
- NULL 的含义必须单一明确，避免同时表示“未填写、无权限、未初始化”等多个状态。
- 不要为了规避 NULL 随意使用空字符串或魔法数字，这会把语义问题转成数据质量问题。

## 常见错误

- 使用 `= NULL`。
- 混淆 NULL、空字符串和 0。
- 用 `NOT IN` 时忽略候选集合中的 NULL。
- 外连接后在 `WHERE` 过滤右表列，意外删除补 NULL 的行。
- `COUNT(column)` 被误当作总行数。

## 相关主题

- [运算符与表达式](运算符与表达式.md)
- [COUNT 统计](COUNT-统计.md)
- [LEFT JOIN](LEFT-JOIN.md)
- [NOT NULL 非空约束](../02-Schema-DDL/NOT-NULL-非空约束.md)
