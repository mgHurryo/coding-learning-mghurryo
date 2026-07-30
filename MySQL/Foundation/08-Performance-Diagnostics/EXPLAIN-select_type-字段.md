---
title: EXPLAIN select_type 字段
description: `select_type` 描述查询类型，例如 SIMPLE、PRIMARY、SUBQUERY、DERIVED。
tags:
  - MySQL
  - Performance
category: MySQL
---

# EXPLAIN select_type 字段

## 方法定位

`select_type` 描述查询类型，例如 SIMPLE、PRIMARY、SUBQUERY、DERIVED。

## 基本语法

```sql
EXPLAIN SELECT * FROM (SELECT id, title FROM article) t WHERE t.id > 10;
```

## 示例场景

派生表、子查询和联合查询会让 `select_type` 更复杂。

## 使用边界

适合识别复杂 SQL 结构；性能判断还要结合 type、rows、Extra。

## 常见错误

不要只因出现 SUBQUERY 就判断一定慢，要看是否被优化器改写。

## 调优提示

复杂派生表若导致临时表，可考虑拆查询或补索引。

## 相关主题

- [EXPLAIN id 字段](EXPLAIN-id-字段.md)
- [Subquery 子查询](Subquery-子查询.md)


