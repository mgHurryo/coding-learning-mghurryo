---
title: UNIQUE 唯一约束
description: 用于保证字段或字段组合在表内唯一，常用于业务幂等和去重。
tags:
  - MySQL
  - DDL
category: MySQL
---

# UNIQUE 唯一约束

## 方法定位

用于保证字段或字段组合在表内唯一，常用于业务幂等和去重。

## 基本语法

```sql
UNIQUE KEY uk_user_username (username)
```

## 示例场景

用户表可对 `username` 建唯一约束，注册时避免重复用户名。

## 使用边界

适合业务上必须唯一的字段；不适合低质量、经常变化且不真正唯一的字段。

## 常见错误

不要只靠应用查询判断唯一；并发写入下必须用数据库唯一约束兜底。

## 调优提示

唯一索引既是约束也是索引，可支持等值查询和幂等写入。

## 相关主题

- [[MySQL/05-Indexing/Unique-Index-唯一索引|Unique Index 唯一索引]]
- [[MySQL/03-SQL-DML/INSERT-ON-DUPLICATE-KEY-UPDATE|INSERT ON DUPLICATE KEY UPDATE]]


