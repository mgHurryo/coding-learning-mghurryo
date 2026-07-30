---
title: EXPLAIN Extra 字段
description: `Extra` 展示额外执行信息，例如 Using index、Using where、Using filesort、Using temporary。
tags:
  - MySQL
  - Performance
category: MySQL
---

# EXPLAIN Extra 字段

## 方法定位

`Extra` 展示额外执行信息，例如 Using index、Using where、Using filesort、Using temporary。

## 基本语法

```sql
EXPLAIN SELECT user_id, COUNT(*) FROM article GROUP BY user_id;
```

## 示例场景

看到 `Using filesort` 或 `Using temporary` 时，应检查排序、分组和索引设计。

## 使用边界

适合作为调优提示；某些 Extra 并不必然是坏事。

## 常见错误

不要看到 `Using where` 就紧张，它只是表示服务层仍需过滤。

## 调优提示

针对 `Using filesort` 优化 ORDER BY，针对 `Using temporary` 优化 GROUP BY 或查询结构。

## 相关主题

- [ORDER BY 调优](ORDER-BY-调优.md)
- [GROUP BY 调优](GROUP-BY-调优.md)


