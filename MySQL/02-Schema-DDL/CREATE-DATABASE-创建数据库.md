---
title: CREATE DATABASE 创建数据库
description: 用于创建一个新的 MySQL 数据库，并指定默认字符集和排序规则。
tags:
  - MySQL
  - DDL
category: MySQL
---

# CREATE DATABASE 创建数据库

## 方法定位

用于创建一个新的 MySQL 数据库，并指定默认字符集和排序规则。

## 基本语法

```sql
CREATE DATABASE app_db CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci;
```

## 示例场景

创建后端应用数据库：`CREATE DATABASE big_event CHARACTER SET utf8mb4;`，后续表都放在该库中。

## 使用边界

适合项目初始化、测试环境搭建；生产环境创建库通常需要权限审批和备份策略。

## 常见错误

不要忽略字符集；不要在脚本中反复无保护地创建同名数据库。

## 调优提示

统一库级默认字符集能减少表级重复配置和字符集转换问题。

## 相关主题

- [Database 数据库概念](../01-Foundations/Database-数据库概念.md)
- [Charset 字符集与排序规则](../01-Foundations/Charset-字符集与排序规则.md)


