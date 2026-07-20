---
title: ALTER TABLE 修改表
description: 用于修改表结构，例如添加字段、修改字段类型、添加索引或约束。
tags:
  - MySQL
  - DDL
category: MySQL
---

# ALTER TABLE 修改表

## 方法定位

用于修改表结构，例如添加字段、修改字段类型、添加索引或约束。

## 基本语法

```sql
// 添加数据
ALTER TABLE user ADD COLUMN update_time DATETIME NULL;

// 修改数据类型
ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);

// 修改字段名和字段类型
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释] [约束];

// 删除字段
ALTER TABLE 表名 DROP 字段名;

// 修改表名
ALTER TABLE 表名 RENAME TO 新表名;

// 删除表
DROP TABLE [IF EXISTS] 表名;

// 删除指定表, 并重新创建该表
TRUNCATE TABLE 表名;

```

## 示例场景

给 `article` 表新增 `update_time` 字段，或给 `category_name` 增加唯一索引。

## 使用边界

适合结构演进；大表上线修改要评估执行时间、锁和回滚方案。

## 常见错误

不要直接在业务高峰期改大表；不要随意缩短字段长度导致数据截断。

## 调优提示

大表改结构优先考虑在线 DDL 能力、灰度发布和应用兼容窗口。

## 相关主题

- [CREATE TABLE 创建表](CREATE-TABLE-创建表.md)
- [SHOW INDEX 查看索引](../05-Indexing/SHOW-INDEX-查看索引.md)


