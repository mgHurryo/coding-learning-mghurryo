---
title: RIGHT JOIN
description: 用于保留右表全部记录，并匹配左表数据。
tags:
  - MySQL
  - Query
category: MySQL
---

# RIGHT JOIN

## 方法定位

用于保留右表全部记录，并匹配左表数据。

## 基本语法

```sql
SELECT a.id, c.category_name FROM article a RIGHT JOIN category c ON a.category_id = c.id;
```

## 示例场景

可以表达保留所有分类的查询，但通常改写为 `LEFT JOIN` 更易读。

## 使用边界

语义上可用；团队实践中更推荐统一使用 `LEFT JOIN` 表达主表保留。

## 常见错误

不要在同一项目中混用大量左右连接导致阅读成本增加。

## 调优提示

优化思路与左连接一致，重点是驱动表、过滤条件和连接索引。

## 相关主题

- [LEFT JOIN](LEFT-JOIN.md)
- [JOIN 调优](../08-Performance-Diagnostics/JOIN-调优.md)


