---
title: EXPLAIN rows 字段
description: `rows` 是优化器估算需要扫描的行数，用于判断查询成本量级。
tags:
  - MySQL
  - Performance
category: MySQL
---

# EXPLAIN rows 字段

## 方法定位

`rows` 是优化器估算需要扫描的行数，用于判断查询成本量级。

## 基本语法

```sql
EXPLAIN SELECT * FROM article WHERE status = 1;
```

## 示例场景

如果某接口只想返回 20 条，却估算扫描几十万行，需要检查索引和条件。

## 使用边界

这是估算值，不是实际扫描精确值；仍可作为调优信号。

## 常见错误

不要忽略 rows 与 filtered、Extra 的组合含义。

## 调优提示

降低 rows 的常见办法是提升过滤条件选择性、使用更合适的联合索引。

## 相关主题

- [WHERE 调优](WHERE-调优.md)
- [Composite Index 联合索引](../05-Indexing/Composite-Index-联合索引.md)


