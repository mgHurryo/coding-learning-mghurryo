---
title: Table 表
description: 说明表是 MySQL 存储结构化业务数据的基本单位，由字段、约束、索引和存储引擎共同定义。
tags:
  - MySQL
  - Basics
category: MySQL
---

# Table 表

## 方法定位

说明表是 MySQL 存储结构化业务数据的基本单位，由字段、约束、索引和存储引擎共同定义。

## 基本语法

```sql
CREATE TABLE user (id BIGINT PRIMARY KEY, username VARCHAR(50) NOT NULL);
```

## 示例场景

`user` 表保存用户，`article` 表保存文章，`category` 表保存分类。表设计应围绕业务实体和查询方式展开。

## 使用边界

适合保存结构稳定、需要事务和查询的数据；不适合保存大二进制文件本体。

## 常见错误

不要把多个实体强行塞进一张大宽表；不要忽略主键。

## 调优提示

表字段越清晰，索引和查询越容易优化。高频查询字段应在建表阶段提前识别。

## 相关主题

- [Column 字段](Column-字段.md)
- [CREATE TABLE 创建表](CREATE-TABLE-创建表.md)


