---
title: Charset 字符集与排序规则
description: 说明字符集决定能存哪些字符，排序规则决定字符串比较和排序方式。
tags:
  - MySQL
  - Basics
category: MySQL
---

# Charset 字符集与排序规则

## 方法定位

说明字符集决定能存哪些字符，排序规则决定字符串比较和排序方式。

## 基本语法

```sql
CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci
```

## 示例场景

需要保存中文、Emoji 或多语言内容时，数据库、表、字段建议统一 `utf8mb4`。

## 使用边界

适合在建库建表时统一规划；跨字符集比较和迁移要谨慎。

## 常见错误

不要混用多套字符集；不要使用已经不适合现代应用的 `utf8mb3`。

## 调优提示

字符集不一致可能导致索引比较、排序和连接查询出现额外转换成本。

## 相关主题

- [Database 数据库概念](Database-数据库概念.md)
- [CREATE DATABASE 创建数据库](CREATE-DATABASE-创建数据库.md)


