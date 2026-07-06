---
title: LIMIT 分页
description: 用于限制返回行数，常用于列表分页。
tags:
  - MySQL
  - Query
category: MySQL
---

# LIMIT 分页

## 方法定位

用于限制返回行数，常用于列表分页。

## 基本语法

```sql
SELECT id, title FROM article ORDER BY id DESC LIMIT 20 OFFSET 40;
```

## 示例场景

文章列表每页 20 条时，用 `LIMIT` 控制返回数量。

## 使用边界

适合浅分页；深分页应使用游标或基于主键的 seek pagination。

## 常见错误

不要在大表上直接 `LIMIT 100000, 20`；不要省略稳定排序字段。

## 调优提示

深分页改写为 `WHERE id < last_id ORDER BY id DESC LIMIT 20` 通常更稳定。

## 相关主题

- [[MySQL/08-Performance-Diagnostics/LIMIT-深分页调优|LIMIT 深分页调优]]
- [[MySQL/04-Query-Methods/ORDER-BY-排序|ORDER BY 排序]]


