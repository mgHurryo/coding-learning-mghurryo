---
title: Index 索引概念
description: 索引用于加速查询定位，也会影响排序、分组、唯一性和写入成本。
tags:
  - MySQL
  - Index
category: MySQL
---

# Index 索引概念

## 方法定位

索引用于加速查询定位，也会影响排序、分组、唯一性和写入成本。

## 基本语法

```sql
CREATE INDEX idx_article_user_id ON article(user_id);
```

## 示例场景

文章表按 `user_id` 查询列表时，为 `user_id` 建索引可以减少全表扫描。

## 使用边界

适合高频查询、排序、关联字段；不适合为每个字段都建索引。

## 常见错误

不要只看是否有索引，还要看查询是否真正使用索引。

## 调优提示

索引提升读性能但增加写成本，应围绕真实查询模式设计。

## 相关主题

- [[MySQL/05-Indexing/Composite-Index-联合索引|Composite Index 联合索引]]
- [[MySQL/08-Performance-Diagnostics/EXPLAIN-使用方法|EXPLAIN 使用方法]]


