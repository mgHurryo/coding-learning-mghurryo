---
title: BETWEEN 范围查询
description: 用于表达闭区间范围条件，常见于时间、金额、ID 范围。
tags:
  - MySQL
  - Query
category: MySQL
---

# BETWEEN 范围查询

## 方法定位

用于表达闭区间范围条件，常见于时间、金额、ID 范围。

## 基本语法

```sql
SELECT id, title FROM article WHERE create_time BETWEEN "2026-01-01" AND "2026-01-31";
```

## 示例场景

查询 2026 年 1 月创建的文章。

## 使用边界

适合闭区间；时间查询更常用半开区间避免边界精度问题。

## 常见错误

不要忽略 `BETWEEN` 包含左右边界；不要对时间字段先格式化再比较。

## 调优提示

范围条件通常放在联合索引较靠后位置，范围之后的列可能无法继续充分利用索引。

## 相关主题

- [Leftmost Prefix 最左前缀原则](../05-Indexing/Leftmost-Prefix-最左前缀原则.md)
- [WHERE 调优](../08-Performance-Diagnostics/WHERE-调优.md)


