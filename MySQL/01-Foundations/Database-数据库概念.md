---
title: Database 数据库概念
description: 说明 MySQL 中数据库 schema 的职责：承载一组业务相关的表、视图、索引和权限边界。
tags:
  - MySQL
  - Basics
category: MySQL
---

# Database 数据库概念

## 方法定位

说明 MySQL 中数据库 schema 的职责：承载一组业务相关的表、视图、索引和权限边界。

## 基本语法

```sql
CREATE DATABASE app_db CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci;
```

## 示例场景

后端项目通常为一个应用创建一个独立数据库，例如 `big_event`，再在其中创建 `user`、`article`、`category` 等表。

## 使用边界

适合按应用或业务域隔离数据；不适合把完全无关的系统长期混放在同一个数据库中。

## 常见错误

不要在应用代码里硬编码生产数据库名；不要用测试库承载线上数据。

## 调优提示

数据库级字符集会影响默认表字符集，建议新项目统一使用 `utf8mb4`。

## 相关主题

- [[MySQL/01-Foundations/Charset-字符集与排序规则|Charset 字符集与排序规则]]
- [[MySQL/02-Schema-DDL/CREATE-DATABASE-创建数据库|CREATE DATABASE 创建数据库]]


