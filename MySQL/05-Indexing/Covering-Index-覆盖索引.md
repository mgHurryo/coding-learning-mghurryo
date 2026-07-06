---
title: Covering Index 覆盖索引
description: 说明覆盖索引如何通过索引本身满足查询字段，减少回表成本。
tags:
  - MySQL
  - Index
category: MySQL
---

# Covering Index 覆盖索引

## 速览

覆盖索引是指查询所需字段都能从索引中取得，不需要再回聚簇索引读取完整行。它能显著减少大量二级索引查询的回表成本。

## 核心机制

InnoDB 二级索引叶子节点保存索引列和主键值。如果 SELECT 字段都在二级索引中，执行计划 Extra 可能出现 `Using index`。覆盖索引常用于列表页、分页查询和统计查询。

## SQL/配置示例

```sql
CREATE INDEX idx_article_user_status_time_title
ON article(user_id, status, create_time, title);

EXPLAIN SELECT id, title
FROM article
WHERE user_id = 1 AND status = 1
ORDER BY create_time DESC
LIMIT 20;
```

## 项目落地

文章列表只展示 id、title、create_time 时，可以围绕筛选、排序和展示字段设计覆盖索引。正文、JSON、大文本字段不要轻易放进索引。

## 常见错误

不要为了覆盖所有查询创建很宽的索引；宽索引会增加写入成本、页分裂、缓存占用和维护成本。

## 面试追问

- 覆盖索引和回表是什么关系？
- Extra 中 `Using index` 代表什么？
- 为什么不能无限追求覆盖索引？

## 排障/边界

当慢查询大量回表时，覆盖索引是候选方案之一；但若查询本身返回大量行，仍要考虑分页、业务限制和查询模式调整。

## 相关主题

- [[MySQL/99-Common-Concepts/回表|回表]]
- [[MySQL/05-Indexing/Secondary-Index-二级索引|Secondary Index 二级索引]]
- [[MySQL/08-Performance-Diagnostics/EXPLAIN-Extra-字段|EXPLAIN Extra 字段]]
