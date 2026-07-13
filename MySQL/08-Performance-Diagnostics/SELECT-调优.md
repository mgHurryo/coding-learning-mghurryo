---
title: SELECT 调优
description: 围绕查询字段、过滤条件、索引、分页和返回数据量优化 SELECT。
tags:
  - MySQL
  - Performance
category: MySQL
---

# SELECT 调优

## 方法定位

围绕查询字段、过滤条件、索引、分页和返回数据量优化 SELECT。

## 基本语法

```sql
SELECT id, title FROM article WHERE user_id = 1 ORDER BY id DESC LIMIT 20;
```

## 示例场景

文章列表接口只取展示字段，不返回正文大字段。

## 使用边界

适合读接口性能优化；写入瓶颈要看事务、锁和批量策略。

## 常见错误

不要 `SELECT *`；不要一次返回过多行；不要忽略慢 SQL 的业务调用频率。

## 调优提示

减少列、减少行、命中索引、稳定排序、必要时缓存或预计算。

## 相关主题

- [SELECT 基础查询](../03-SQL-DML/SELECT-基础查询.md)
- [LIMIT 深分页调优](LIMIT-深分页调优.md)


