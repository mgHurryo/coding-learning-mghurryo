---
title: Aggregate 聚合函数
description: 说明 `SUM`、`AVG`、`MAX`、`MIN` 等聚合函数的用途。
tags:
  - MySQL
  - Query
category: MySQL
---

# Aggregate 聚合函数

## 方法定位

说明 `SUM`、`AVG`、`MAX`、`MIN` 等聚合函数的用途。

## 基本语法

```sql
SELECT category_id, COUNT(*) AS total, MAX(create_time) AS latest FROM article GROUP BY category_id;
```

## 示例场景

按分类统计文章数，并找出每个分类最新发布时间。

## 使用边界

适合报表和汇总；复杂分析应考虑离线数仓或专门统计表。

## 常见错误

不要在聚合查询中选择未分组且无聚合的字段；不要忽略 NULL 对聚合结果的影响。

## 调优提示

聚合前尽量用 `WHERE` 减少输入行数，必要时为分组字段建索引。

## 相关主题

- [[MySQL/04-Query-Methods/GROUP-BY-分组|GROUP BY 分组]]
- [[MySQL/08-Performance-Diagnostics/GROUP-BY-调优|GROUP BY 调优]]


