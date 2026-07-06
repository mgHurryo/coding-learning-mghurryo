---
title: IN 多值匹配
description: 用于判断字段是否属于一组候选值。
tags:
  - MySQL
  - Query
category: MySQL
---

# IN 多值匹配

## 方法定位

用于判断字段是否属于一组候选值。

## 基本语法

```sql
SELECT id, username FROM user WHERE id IN (1, 2, 3);
```

## 示例场景

根据接口传入的一组文章 ID 批量查询文章摘要。

## 使用边界

适合候选值数量可控的查询；超大列表应考虑临时表或拆批。

## 常见错误

不要把几千上万个值直接塞进 `IN`；不要忽略空列表导致 SQL 错误。

## 调优提示

`IN` 字段有索引时更稳定；批量查询结果顺序需要额外 `ORDER BY` 或应用重排。

## 相关主题

- [[MySQL/04-Query-Methods/WHERE-条件过滤|WHERE 条件过滤]]
- [[MySQL/08-Performance-Diagnostics/SELECT-调优|SELECT 调优]]


