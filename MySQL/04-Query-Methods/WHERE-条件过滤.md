---
title: WHERE 条件过滤
description: 用于筛选满足条件的行，是查询、更新、删除的安全边界。
tags:
  - MySQL
  - Query
category: MySQL
---

# WHERE 条件过滤

## 方法定位

用于筛选满足条件的行，是查询、更新、删除的安全边界。

## 基本语法

```sql
SELECT id, title FROM article WHERE user_id = 1 AND status = 1;
```

## 示例场景

查询某个用户已发布文章时，同时过滤 `user_id` 和 `status`。

## 使用边界

适合行级过滤；聚合后的过滤应使用 `HAVING`。

## 常见错误

不要对索引列随意使用函数或隐式类型转换；不要忘记租户、用户、逻辑删除条件。

## 调优提示

高频过滤条件是索引设计的核心输入，等值条件通常放在联合索引前部。

## 相关主题

- [[MySQL/08-Performance-Diagnostics/WHERE-调优|WHERE 调优]]
- [[MySQL/05-Indexing/Composite-Index-联合索引|Composite Index 联合索引]]


