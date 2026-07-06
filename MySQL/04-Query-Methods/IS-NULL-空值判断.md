---
title: IS NULL 空值判断
description: 用于判断字段是否为 SQL NULL。
tags:
  - MySQL
  - Query
category: MySQL
---

# IS NULL 空值判断

## 方法定位

用于判断字段是否为 SQL NULL。

## 基本语法

```sql
SELECT id FROM user WHERE last_login_time IS NULL;
```

## 示例场景

查询从未登录过的用户，可判断 `last_login_time IS NULL`。

## 使用边界

适合明确区分未知和空值的字段；业务必填字段应使用 `NOT NULL`。

## 常见错误

不要使用 `= NULL`；不要混用空字符串和 `NULL`。

## 调优提示

可空字段会增加判断复杂度，必要时结合默认值和非空约束优化模型。

## 相关主题

- [[MySQL/02-Schema-DDL/NOT-NULL-非空约束|NOT NULL 非空约束]]
- [[MySQL/02-Schema-DDL/DEFAULT-默认值|DEFAULT 默认值]]


