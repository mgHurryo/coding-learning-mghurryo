---
title: CREATE TABLE 创建表
description: 用于定义表结构，包括字段、主键、约束、索引和存储引擎。
tags:
  - MySQL
  - DDL
category: MySQL
---

# CREATE TABLE 创建表

## 方法定位

用于定义表结构，包括字段、主键、约束、索引和存储引擎。

## 基本语法

```sql
CREATE TABLE user (id BIGINT PRIMARY KEY AUTO_INCREMENT, username VARCHAR(50) NOT NULL, create_time DATETIME NOT NULL) ENGINE=InnoDB;
```

## 示例场景

创建 `user` 表时同时定义 `id`、`username`、`create_time`，并为唯一业务字段补充唯一约束。

## 使用边界

适合项目初始化和版本化迁移；上线后改表要评估锁表和兼容性。

## 常见错误

不要没有主键；不要字段含义模糊；不要把所有字段都设为可空。

## 调优提示

建表时预留常用查询字段的索引设计，能避免后期大表改索引成本。

## 相关主题

- [PRIMARY KEY 主键约束](PRIMARY-KEY-主键约束.md)
- [Index 索引概念](../05-Indexing/Index-索引概念.md)


