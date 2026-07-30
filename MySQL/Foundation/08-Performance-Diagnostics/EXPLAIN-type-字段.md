---
title: EXPLAIN type 字段
description: `type` 表示表访问方式，常用于判断查询从全表扫描到索引定位的效率层级。
tags:
  - MySQL
  - Performance
category: MySQL
---

# EXPLAIN type 字段

## 方法定位

`type` 表示表访问方式，常用于判断查询从全表扫描到索引定位的效率层级。

## 基本语法

```sql
EXPLAIN SELECT * FROM user WHERE id = 1;
```

## 示例场景

主键等值查询通常是 `const`，普通索引范围查询可能是 `range`，全表扫描是 `ALL`。

## 使用边界

适合快速识别访问方式；并非 type 越高就一定整体越快。

## 常见错误

不要忽略小表全表扫描可能比走索引更合理。

## 调优提示

大表核心查询应避免长期出现 `ALL`，优先检查 WHERE 条件和索引。

## 相关主题

- [EXPLAIN key 字段](EXPLAIN-key-字段.md)
- [Index Failure 索引失效场景](Index-Failure-索引失效场景.md)


