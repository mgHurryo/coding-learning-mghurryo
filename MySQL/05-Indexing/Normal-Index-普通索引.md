---
title: Normal Index 普通索引
description: 普通索引用于加速非唯一字段查询，不提供唯一性保证。
tags:
  - MySQL
  - Index
category: MySQL
---

# Normal Index 普通索引

## 方法定位

普通索引用于加速非唯一字段查询，不提供唯一性保证。

## 基本语法

```sql
CREATE INDEX idx_article_category ON article(category_id);
```

## 示例场景

按分类查询文章列表时，为 `category_id` 建普通索引。

## 使用边界

适合高频过滤字段；低选择性字段单独建索引收益有限。

## 常见错误

不要给很少过滤、很少排序的字段建冗余索引。

## 调优提示

普通索引常与排序或其他过滤字段组成联合索引提升收益。

## 相关主题

- [[MySQL/05-Indexing/Composite-Index-联合索引|Composite Index 联合索引]]
- [[MySQL/08-Performance-Diagnostics/WHERE-调优|WHERE 调优]]


