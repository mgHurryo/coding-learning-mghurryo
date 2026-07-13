---
title: COUNT 统计
description: 用于统计行数或非空值数量。
tags:
  - MySQL
  - Query
category: MySQL
---

# COUNT 统计

## 方法定位

用于统计行数或非空值数量。

## 基本语法

```sql
SELECT COUNT(*) FROM article WHERE user_id = 1 AND deleted = 0;
```

## 示例场景

统计用户未删除文章数量，用于个人主页计数。

## 使用边界

适合精确计数；超大表高频总数展示可考虑缓存或异步统计。

## 常见错误

不要把频繁全表 `COUNT(*)` 放在高并发接口中；不要误解 `COUNT(column)` 会忽略 NULL。

## 调优提示

带条件计数应命中合适索引；总数类需求可用汇总表降低实时压力。

## 相关主题

- [SELECT 调优](../08-Performance-Diagnostics/SELECT-调优.md)
- [GROUP BY 分组](GROUP-BY-分组.md)


