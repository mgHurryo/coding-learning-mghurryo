---
title: NOT NULL 非空约束
description: 用于要求字段必须有值，减少业务判断中的未知状态。
tags:
  - MySQL
  - DDL
category: MySQL
---

# NOT NULL 非空约束

## 方法定位

用于要求字段必须有值，减少业务判断中的未知状态。

## 基本语法

```sql
username VARCHAR(50) NOT NULL
```

## 示例场景

用户名、创建时间、状态字段通常不应为空，避免查询和展示逻辑复杂化。

## 使用边界

适合业务必填字段；对历史兼容或确实可缺失的信息可允许 `NULL`。

## 常见错误

不要把空字符串和 `NULL` 混用表示同一种含义。

## 调优提示

非空字段能让优化器统计更稳定，也能简化索引条件判断。

## 相关主题

- [DEFAULT 默认值](DEFAULT-默认值.md)
- [IS NULL 空值判断](IS-NULL-空值判断.md)


