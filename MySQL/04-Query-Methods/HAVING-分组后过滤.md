---
title: HAVING 分组后过滤
description: 讲解 HAVING 对聚合结果的过滤、与 WHERE 的执行阶段差异、别名和性能要点。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# HAVING 分组后过滤

## 方法定位

`HAVING` 在 `GROUP BY` 之后过滤分组，主要用于聚合条件，例如“文章数至少 10 的用户”。

## 基本语法

```sql

SELECT
    user_id,
    COUNT(*) AS article_count
FROM article
GROUP BY user_id
HAVING COUNT(*) > 10;
```

MySQL 通常允许在 `HAVING` 中使用输出别名：

```sql

SELECT user_id, COUNT(*) AS article_count
FROM article
GROUP BY user_id
HAVING article_count > 10;
```

重复聚合表达式更接近标准语义，别名更便于阅读；团队应保持一致风格。

## WHERE 与 HAVING 分工

```sql

SELECT
    category_id,
    COUNT(*) AS article_count,
    AVG(view_count) AS average_views
FROM article
WHERE status = 1
GROUP BY category_id
HAVING COUNT(*) >= 10
   AND AVG(view_count) >= 1000;
```

- `WHERE status = 1`：聚合前只保留已发布文章。
- `HAVING COUNT(*) >= 10`：聚合后按文章数过滤分类。
- `HAVING AVG(view_count) >= 1000`：聚合后按平均阅读量过滤分类。

能在分组前判断的原始行条件优先写在 `WHERE`，既清楚又可能减少聚合输入。

## 没有 GROUP BY 的 HAVING

没有 `GROUP BY` 时，整个结果可视为一个组：

```sql

SELECT COUNT(*) AS article_count
FROM article
HAVING COUNT(*) > 0;
```

这可以使用，但日常业务查询仍应选择语义最直接的写法。

## HAVING 不是 WHERE 的替代品

```sql

-- 不推荐：status 是原始行条件
SELECT user_id, COUNT(*)
FROM article
GROUP BY user_id
HAVING status = 1;
```

这既可能违反严格分组规则，也可能产生错误语义。应写在 `WHERE`。

## 多个聚合条件

```sql

SELECT
    user_id,
    COUNT(*) AS article_count,
    SUM(view_count) AS total_views
FROM article
WHERE status = 1
GROUP BY user_id
HAVING COUNT(*) BETWEEN 5 AND 20
   AND SUM(view_count) >= 10000;
```

`HAVING` 中同样可以使用 `AND`、`OR` 和括号。

## 性能基础

- 尽可能用 `WHERE` 先减少输入行。
- `HAVING` 依赖聚合结果时，数据库通常必须先完成相应分组计算。
- 分组字段、过滤字段与聚合范围共同决定索引策略。
- 大范围统计应通过执行计划和实际耗时验证。

## 常见错误

- 把明细行条件全部放 `HAVING`。
- 在 `WHERE` 中使用聚合函数。
- 没有定义分组粒度。
- 使用未分组字段作为 `HAVING` 条件。
- 混用 `AND` 与 `OR` 不加括号。

## 相关主题

- [SELECT 逻辑执行顺序](SELECT-逻辑执行顺序.md)
- [WHERE 条件过滤](WHERE-条件过滤.md)
- [GROUP BY 分组](GROUP-BY-分组.md)
- [Aggregate 聚合函数](Aggregate-聚合函数.md)
