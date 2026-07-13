---
title: SHOW INDEX 查看索引
description: 用于查看表上已有索引、索引列顺序、唯一性和基数估计。
tags:
  - MySQL
  - Index
category: MySQL
---

# SHOW INDEX 查看索引

## 方法定位

用于查看表上已有索引、索引列顺序、唯一性和基数估计。

## 基本语法

```sql
SHOW INDEX FROM article;
```

## 示例场景

优化文章查询前，先查看 `article` 表已有索引，避免重复创建。

## 使用边界

适合索引盘点；是否使用索引还要看 `EXPLAIN`。

## 常见错误

不要只凭索引名判断用途；要看列顺序和唯一性。

## 调优提示

清理重复或左前缀冗余索引可降低写入维护成本。

## 相关主题

- [Index 索引概念](Index-索引概念.md)
- [EXPLAIN key 字段](../08-Performance-Diagnostics/EXPLAIN-key-字段.md)


