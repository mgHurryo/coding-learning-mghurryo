---
title: ORDER BY 排序
description: 用于指定结果集排序方式。
tags:
  - MySQL
  - Query
category: MySQL
---

# ORDER BY 排序

## 方法定位

用于指定结果集排序方式。

## 基本语法

```sql
SELECT id, title FROM article WHERE user_id = 1 ORDER BY create_time DESC;
```

## 示例场景

文章列表通常按创建时间或更新时间倒序展示。

## 使用边界

适合明确排序需求；无排序时数据库不保证返回顺序。

## 常见错误

不要依赖默认返回顺序；不要在大结果集上对非索引列排序。

## 调优提示

排序字段与过滤字段可设计联合索引，减少 `Using filesort`。

## 相关主题

- [ORDER BY 调优](../08-Performance-Diagnostics/ORDER-BY-调优.md)
- [Composite Index 联合索引](../05-Indexing/Composite-Index-联合索引.md)


