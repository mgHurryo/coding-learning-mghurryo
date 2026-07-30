---
title: EXPLAIN id 字段
description: `id` 表示查询中 SELECT 的执行层级和顺序参考。
tags:
  - MySQL
  - Performance
category: MySQL
---

# EXPLAIN id 字段

## 方法定位

`id` 表示查询中 SELECT 的执行层级和顺序参考。

## 基本语法

```sql
EXPLAIN SELECT * FROM article WHERE user_id IN (SELECT id FROM user WHERE status = 1);
```

## 示例场景

分析子查询时，`id` 可以帮助区分外层查询和内层查询。

## 使用边界

适合理解复杂查询结构；简单单表查询通常只有一个 id。

## 常见错误

不要机械认为 id 越大就一定越先执行，优化器可能重写查询。

## 调优提示

复杂查询先关注是否能简化为 JOIN 或拆分，再看各层扫描成本。

## 相关主题

- [Subquery 子查询](Subquery-子查询.md)
- [EXPLAIN 使用方法](EXPLAIN-使用方法.md)


