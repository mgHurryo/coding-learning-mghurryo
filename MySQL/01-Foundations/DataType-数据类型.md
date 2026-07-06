---
title: DataType 数据类型
description: 梳理整数、字符串、时间、定点数等常见类型的选择方法。
tags:
  - MySQL
  - Basics
category: MySQL
---

# DataType 数据类型

## 方法定位

梳理整数、字符串、时间、定点数等常见类型的选择方法。

## 基本语法

```sql
id BIGINT AUTO_INCREMENT, amount DECIMAL(10,2), create_time DATETIME
```

## 示例场景

金额用 `DECIMAL`，自增主键常用 `BIGINT`，时间字段常用 `DATETIME` 或 `TIMESTAMP`。

## 使用边界

类型选择应依据值域、精度、排序和索引需求；不应只为了省事全部使用字符串。

## 常见错误

金额不要用 `FLOAT`；时间不要随意用字符串保存；状态值不要无限制使用长文本。

## 调优提示

更小且准确的类型能减少页占用，提升索引缓存命中率。

## 相关主题

- [[MySQL/01-Foundations/Column-字段|Column 字段]]
- [[MySQL/02-Schema-DDL/CREATE-TABLE-创建表|CREATE TABLE 创建表]]


