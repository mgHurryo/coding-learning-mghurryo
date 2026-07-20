---
title: GROUP BY 分组
description: 讲解分组粒度、聚合、ONLY_FULL_GROUP_BY、NULL 分组、多列分组和连接放大。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# GROUP BY 分组

## 方法定位

`GROUP BY` 将具有相同分组键的明细行归为一组，通常配合聚合函数让每组输出一行。

## 基本语法

```sql

SELECT
    category_id,
    COUNT(*) AS article_count,
    SUM(view_count) AS total_views
FROM article
WHERE status = 1
GROUP BY category_id;
```

查询的结果粒度是“每个分类一行”。

## 先确定结果粒度

写分组查询前先用一句话定义结果：

- 每个用户一行：`GROUP BY user_id`。
- 每个用户、每个分类一行：`GROUP BY user_id, category_id`。
- 每天一行：按日期表达式或预先建模的日期字段分组。

多加一个分组列会细化结果，少一个则会合并原本不同的数据。

## 多列分组

```sql

SELECT
    user_id,
    category_id,
    COUNT(*) AS article_count
FROM article
GROUP BY user_id, category_id;
```

分组键是 `(user_id, category_id)` 的组合，不是分别产生两套统计。

## 严格分组规则

MySQL 8 默认常启用 `ONLY_FULL_GROUP_BY`。输出列应满足：

1. 是聚合表达式；或
2. 出现在 `GROUP BY` 中；或
3. MySQL 能根据约束判断其函数依赖于分组列。

错误示例：

```sql

SELECT user_id, title, COUNT(*)
FROM article
GROUP BY user_id;
```

一个用户可能有多篇不同标题，数据库无法判断应返回哪个标题。应修改结果粒度、使用明确聚合，或先用窗口函数定义“取哪一篇”。

不要关闭严格模式来掩盖不确定查询。

## NULL 如何分组

所有 NULL 分组键会归入同一组：

```sql

SELECT category_id, COUNT(*)
FROM article
GROUP BY category_id;
```

`category_id IS NULL` 的文章形成一个分组。是否应该存在这组取决于数据模型与业务含义。

## WHERE 与 HAVING

```sql

SELECT user_id, COUNT(*) AS article_count
FROM article
WHERE status = 1
GROUP BY user_id
HAVING COUNT(*) >= 10;
```

- `WHERE` 在分组前过滤文章。
- `HAVING` 在分组后过滤用户组。

## 按表达式分组

```sql

SELECT DATE(create_time) AS publish_date, COUNT(*) AS article_count
FROM article
WHERE create_time >= '2026-07-01'
  AND create_time <  '2026-08-01'
GROUP BY DATE(create_time);
```

过滤条件仍对原列使用范围，利于索引定位；分组表达式可能需要额外计算。高频按天统计可考虑生成列、日期维度或汇总表。

## 分组不保证排序

`GROUP BY` 不应被当作 `ORDER BY` 使用。需要顺序时显式写：

```sql

ORDER BY article_count DESC, user_id ASC
```

## 连接后的分组

连接多个一对多关系时，先检查结果是否交叉放大。常用策略是各子表先按目标粒度预聚合，再连接聚合结果。

## 性能基础

- `WHERE` 尽量减少输入行。
- 索引可能协助过滤和分组，但列顺序必须匹配访问路径。
- 执行计划出现临时表或 filesort 不一定错误，应结合数据量与耗时。
- 大规模实时报表可使用汇总表或离线分析系统。

## 常见错误

- 结果粒度没有先定义。
- 选择未分组且未聚合的字段。
- 依赖 GROUP BY 的偶然顺序。
- 连接放大后直接计数。
- 为通过 SQL 而关闭 `ONLY_FULL_GROUP_BY`。

## 相关主题

- [Aggregate 聚合函数](Aggregate-聚合函数.md)
- [HAVING 分组后过滤](HAVING-分组后过滤.md)
- [Window Functions 窗口函数](Window-Functions-窗口函数.md)
- [GROUP BY 调优](../08-Performance-Diagnostics/GROUP-BY-调优.md)
