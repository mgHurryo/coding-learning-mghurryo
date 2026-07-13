---
title: ORDER BY 调优
description: 优化排序字段和索引，减少额外排序成本。
tags:
  - MySQL
  - Performance
category: MySQL
---

# ORDER BY 调优

## 方法定位

优化排序字段和索引，减少额外排序成本。

## 基本语法

```sql
SELECT id, title FROM article WHERE user_id = 1 ORDER BY create_time DESC LIMIT 20;
```

## 示例场景

用户文章列表按创建时间倒序，适合建立 `(user_id, create_time)` 联合索引。

## 使用边界

适合列表页排序；复杂多字段动态排序要控制可选字段。

## 常见错误

不要允许用户任意传入排序列直接拼 SQL；不要在大结果集上无索引排序。

## 调优提示

WHERE 等值列 + ORDER BY 列组成联合索引，通常能减少 `Using filesort`。

## 相关主题

- [ORDER BY 排序](../04-Query-Methods/ORDER-BY-排序.md)
- [EXPLAIN Extra 字段](EXPLAIN-Extra-字段.md)


