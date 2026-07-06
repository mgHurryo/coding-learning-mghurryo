---
title: Soft Delete 逻辑删除
description: 通过字段标记记录已删除，而不是物理删除数据。
tags:
  - MySQL
  - DML
category: MySQL
---

# Soft Delete 逻辑删除

## 方法定位

通过字段标记记录已删除，而不是物理删除数据。

## 基本语法

```sql
UPDATE article SET deleted = 1, update_time = NOW() WHERE id = 10;
```

## 示例场景

文章删除后仍需审计或恢复时，将 `deleted` 改为 `1`，查询默认过滤 `deleted = 0`。

## 使用边界

适合需要审计、恢复、关联保留的业务；不适合无限制保留所有历史垃圾数据。

## 常见错误

不要忘记所有查询都过滤删除标记；不要让唯一约束与逻辑删除冲突。

## 调优提示

逻辑删除字段常进入联合索引，但低选择性字段应与业务查询条件组合使用。

## 相关主题

- [[MySQL/04-Query-Methods/WHERE-条件过滤|WHERE 条件过滤]]
- [[MySQL/05-Indexing/Composite-Index-联合索引|Composite Index 联合索引]]


