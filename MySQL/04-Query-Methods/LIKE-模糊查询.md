---
title: LIKE 模糊查询
description: 用于按字符串模式匹配数据，常见于名称搜索和关键词过滤。
tags:
  - MySQL
  - Query
category: MySQL
---

# LIKE 模糊查询

## 方法定位

用于按字符串模式匹配数据，常见于名称搜索和关键词过滤。

## 基本语法

```sql
SELECT id, title FROM article WHERE title LIKE "MySQL%";
```

## 示例场景

搜索标题以 `MySQL` 开头的文章，可以使用右模糊匹配。

## 使用边界

右模糊通常可利用索引；左模糊和全模糊更适合全文检索或搜索引擎。

## 常见错误

不要在大表上随意使用 `LIKE "%keyword%"`；不要把模糊查询当万能搜索。

## 调优提示

能改成前缀匹配就避免全模糊；复杂搜索考虑 Elasticsearch 或 MySQL FULLTEXT。

## 相关主题

- [Index Failure 索引失效场景](../05-Indexing/Index-Failure-索引失效场景.md)
- [WHERE 调优](../08-Performance-Diagnostics/WHERE-调优.md)


