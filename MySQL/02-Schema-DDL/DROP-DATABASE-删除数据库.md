---
title: DROP DATABASE 删除数据库
description: 用于删除整个数据库及其包含的表、数据和对象，是高风险 DDL 操作。
tags:
  - MySQL
  - DDL
category: MySQL
---

# DROP DATABASE 删除数据库

## 方法定位

用于删除整个数据库及其包含的表、数据和对象，是高风险 DDL 操作。

## 基本语法

```sql
DROP DATABASE app_db;
```

## 示例场景

本地重新初始化练习库时可以删除测试库，但生产环境必须先确认备份和影响范围。

## 使用边界

只适合确认不再需要整库数据的场景；不适合替代清理单表数据。

## 常见错误

不要在未确认环境和库名时执行；不要把生产库删除语句写进普通初始化脚本。

## 调优提示

删除整库不是性能调优手段，通常用于环境重建。

## 相关主题

- [CREATE DATABASE 创建数据库](CREATE-DATABASE-创建数据库.md)
- [TRUNCATE 与 DELETE 区别](../03-SQL-DML/TRUNCATE-与-DELETE-区别.md)


