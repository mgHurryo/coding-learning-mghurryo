---
title: Unique Index 唯一索引
description: 唯一索引既加速查询，又保证字段或字段组合不重复。
tags:
  - MySQL
  - Index
category: MySQL
---

# Unique Index 唯一索引

## 方法定位

唯一索引既加速查询，又保证字段或字段组合不重复。

## 基本语法

```sql
CREATE UNIQUE INDEX uk_user_username ON user(username);
```

## 示例场景

注册用户名时，唯一索引防止并发插入同名用户。

## 使用边界

适合业务必须唯一的字段；允许多 NULL 的行为要按 MySQL 规则确认。

## 常见错误

不要只在应用层查重；不要忘记捕获唯一键冲突异常。

## 调优提示

唯一索引等值查询选择性高，通常能快速定位记录。

## 相关主题

- [UNIQUE 唯一约束](../02-Schema-DDL/UNIQUE-唯一约束.md)
- [INSERT ON DUPLICATE KEY UPDATE](../03-SQL-DML/INSERT-ON-DUPLICATE-KEY-UPDATE.md)


