---
title: WHERE 调优
description: 优化过滤条件写法，让 MySQL 尽量利用索引并减少扫描行数。
tags:
  - MySQL
  - Performance
category: MySQL
---

# WHERE 调优

## 方法定位

优化过滤条件写法，让 MySQL 尽量利用索引并减少扫描行数。

## 基本语法

```sql
SELECT id FROM user WHERE username = "tom";
```

## 示例场景

登录查询应使用用户名或邮箱唯一索引定位用户。

## 使用边界

适合条件查询优化；无明确过滤条件的分析类查询可能需要别的架构。

## 常见错误

不要对索引列做函数计算；不要让字段发生隐式类型转换；不要用低选择性条件孤立建索引。

## 调优提示

字段裸露、类型一致、联合索引顺序合理，是 WHERE 调优的基本线。

## 相关主题

- [[MySQL/04-Query-Methods/WHERE-条件过滤|WHERE 条件过滤]]
- [[MySQL/05-Indexing/Index-Failure-索引失效场景|Index Failure 索引失效场景]]


