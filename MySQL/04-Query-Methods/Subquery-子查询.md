---
title: Subquery 子查询
description: 用于在一个查询中嵌套另一个查询，作为条件、派生表或标量值。
tags:
  - MySQL
  - Query
category: MySQL
---

# Subquery 子查询

## 方法定位

用于在一个查询中嵌套另一个查询，作为条件、派生表或标量值。

## 基本语法

```sql
SELECT id, title FROM article WHERE user_id IN (SELECT id FROM user WHERE status = 1);
```

## 示例场景

查询活跃用户发布的文章。

## 使用边界

适合表达分步逻辑；复杂子查询可评估改写为 JOIN 或临时结果。

## 常见错误

不要写无法利用索引的相关子查询；不要让子查询返回超大集合。

## 调优提示

用 `EXPLAIN` 比较子查询与 JOIN 改写后的执行计划。

## 相关主题

- [[MySQL/04-Query-Methods/IN-多值匹配|IN 多值匹配]]
- [[MySQL/08-Performance-Diagnostics/EXPLAIN-使用方法|EXPLAIN 使用方法]]


