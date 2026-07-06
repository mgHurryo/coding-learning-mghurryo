---
title: LEFT JOIN
description: 用于保留左表全部记录，并匹配右表数据。
tags:
  - MySQL
  - Query
category: MySQL
---

# LEFT JOIN

## 方法定位

用于保留左表全部记录，并匹配右表数据。

## 基本语法

```sql
SELECT c.id, c.category_name, a.id AS article_id FROM category c LEFT JOIN article a ON a.category_id = c.id;
```

## 示例场景

展示所有分类，即使某些分类下暂时没有文章。

## 使用边界

适合主表必须保留的场景；右表条件放错位置会改变结果语义。

## 常见错误

不要把右表过滤条件随意放到 `WHERE`，可能把左连接变成内连接效果。

## 调优提示

左表过滤越早越好，右表连接字段应建立索引。

## 相关主题

- [[MySQL/04-Query-Methods/INNER-JOIN|INNER JOIN]]
- [[MySQL/08-Performance-Diagnostics/JOIN-调优|JOIN 调优]]


