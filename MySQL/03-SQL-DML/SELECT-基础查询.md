---
title: SELECT 基础查询
description: 用于从表中读取数据，是查询、统计和业务展示的基础。
tags:
  - MySQL
  - DML
category: MySQL
---

# SELECT 基础查询

## 方法定位

用于从表中读取数据，是查询、统计和业务展示的基础。

## 基本语法

```sql
SELECT id, username, create_time FROM user WHERE id = 1;
```

## 示例场景

登录后根据用户 ID 查询用户基础信息，接口只返回需要的字段。

## 使用边界

适合读取明确字段；复杂过滤、聚合、连接应拆到对应查询方法学习。

## 常见错误

不要默认 `SELECT *`；不要没有条件地读取大表全部数据。

## 调优提示

只选择必要字段，优先让查询命中合适索引或覆盖索引。

## 相关主题

- [WHERE 条件过滤](../04-Query-Methods/WHERE-条件过滤.md)
- [SELECT 调优](../08-Performance-Diagnostics/SELECT-调优.md)


