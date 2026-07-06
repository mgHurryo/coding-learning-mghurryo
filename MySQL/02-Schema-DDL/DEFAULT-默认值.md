---
title: DEFAULT 默认值
description: 用于在插入时未提供字段值的情况下自动填充默认值。
tags:
  - MySQL
  - DDL
category: MySQL
---

# DEFAULT 默认值

## 方法定位

用于在插入时未提供字段值的情况下自动填充默认值。

## 基本语法

```sql
status TINYINT NOT NULL DEFAULT 1
```

## 示例场景

`article.status` 默认 `1` 表示草稿或启用，`deleted` 默认 `0` 表示未删除。

## 使用边界

适合稳定的业务初始状态；不适合需要复杂计算的默认值。

## 常见错误

不要让默认值掩盖业务必填缺失；不要用魔法数字而不写清含义。

## 调优提示

合理默认值可以减少应用插入字段数量，并保持索引字段分布可控。

## 相关主题

- [[MySQL/02-Schema-DDL/NOT-NULL-非空约束|NOT NULL 非空约束]]
- [[MySQL/03-SQL-DML/INSERT-单行插入|INSERT 单行插入]]


