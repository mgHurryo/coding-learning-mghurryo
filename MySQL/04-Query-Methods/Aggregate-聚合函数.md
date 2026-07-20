---
title: Aggregate 聚合函数
description: 系统讲解 COUNT、SUM、AVG、MAX、MIN 的分组语义、NULL 行为、类型和条件聚合。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# Aggregate 聚合函数

## 作用

聚合函数将多行值汇总为一个结果。没有 `GROUP BY` 时，满足条件的所有行视为一个组；有 `GROUP BY` 时，每组产生聚合结果。

## 常用函数

| 函数 | 功能 | NULL 行为 |
| :--- | :--- | :--- |
| `COUNT(*)` | 统计行数 | 统计所有行 |
| `COUNT(expr)` | 统计表达式非 NULL 的行数 | 忽略 NULL |
| `SUM(expr)` | 求和 | 忽略 NULL |
| `AVG(expr)` | 平均值 | 忽略 NULL |
| `MAX(expr)` | 最大值 | 忽略 NULL |
| `MIN(expr)` | 最小值 | 忽略 NULL |

## 全表汇总

```sql

SELECT
    COUNT(*) AS article_count,
    SUM(view_count) AS total_views,
    AVG(view_count) AS average_views,
    MAX(view_count) AS max_views,
    MIN(view_count) AS min_views
FROM article
WHERE status = 1;
```

聚合前先用 `WHERE` 限定业务范围。

## 分组汇总

```sql

SELECT
    category_id,
    COUNT(*) AS article_count,
    MAX(create_time) AS latest_time
FROM article
WHERE status = 1
GROUP BY category_id;
```

每个分类返回一行。输出中的非聚合列必须符合 `GROUP BY` 规则，详见 [GROUP BY 分组](GROUP-BY-分组.md)。

## 空输入与 NULL

当没有满足条件的行时：

- `COUNT(*)` 返回 0。
- `SUM`、`AVG`、`MAX`、`MIN` 通常返回 NULL。

需要展示为 0 时可在输出层使用：

```sql

SELECT COALESCE(SUM(view_count), 0) AS total_views
FROM article
WHERE status = 99;
```

先确认“没有数据”应否等同于 0；两者在某些统计语义中不同。

## 条件聚合

```sql

SELECT
    user_id,
    COUNT(*) AS total_count,
    SUM(CASE WHEN status = 1 THEN 1 ELSE 0 END) AS published_count,
    SUM(CASE WHEN status = 0 THEN 1 ELSE 0 END) AS draft_count
FROM article
GROUP BY user_id;
```

一次分组可计算多个条件口径。条件定义必须互斥或明确允许重叠。

## DISTINCT 聚合

```sql

SELECT COUNT(DISTINCT user_id) AS author_count
FROM article
WHERE status = 1;
```

去重聚合通常比普通聚合需要更多工作。确认真正需要去重，并检查是否因错误连接导致重复。

## 数据类型与精度

- `AVG` 与 `SUM` 的返回类型受输入类型影响。
- 金额字段应使用 `DECIMAL`，避免浮点误差。
- 大量整数求和可能超过业务层目标类型范围，Java 映射也要匹配。
- 平均值由非 NULL 值的总和除以非 NULL 数量。

## 性能基础

- 聚合前尽早过滤不需要的行。
- 合适索引可能帮助过滤、分组或极值查询。
- 大范围精确聚合仍需要读取大量索引项或数据。
- 报表型复杂统计可考虑汇总表、缓存或数仓，但必须定义刷新与一致性要求。

## 常见错误

- 忽略 NULL 导致计数口径错误。
- 在聚合查询中随意选择未分组字段。
- 连接一对多表后计数被放大。
- 无需求却使用 `DISTINCT` 聚合。
- 把空输入的 NULL 结果与 0 混为一谈。

## 相关主题

- [COUNT 统计](COUNT-统计.md)
- [GROUP BY 分组](GROUP-BY-分组.md)
- [HAVING 分组后过滤](HAVING-分组后过滤.md)
- [CASE 条件表达式](CASE-条件表达式.md)
