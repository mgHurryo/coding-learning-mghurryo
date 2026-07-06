---
title: EXPLAIN 使用方法
description: 说明如何阅读 MySQL EXPLAIN 的关键字段，并把执行计划用于 SQL 调优。
tags:
  - MySQL
  - Performance
  - EXPLAIN
category: MySQL
---

# EXPLAIN 使用方法

## 速览

`EXPLAIN` 是查看 SQL 执行计划的入口。重点看访问方式、候选索引、实际索引、扫描行数和额外操作，而不是只看有没有用索引。

## 核心机制

核心字段包括：`id` 表示执行层级；`select_type` 表示查询类型；`type` 表示访问方式；`possible_keys` 是候选索引；`key` 是实际选择索引；`rows` 是估算扫描行数；`Extra` 显示 filesort、temporary、Using index 等额外信息。

## SQL/配置示例

```sql
EXPLAIN
SELECT id, title
FROM article
WHERE user_id = 1 AND status = 1
ORDER BY create_time DESC
LIMIT 20;
```

## 项目落地

调优接口 SQL 时，把 Mapper SQL、真实参数、表数据量、索引列表和 EXPLAIN 放在一起看。调优后要保存前后对比，避免凭感觉改索引。

## 常见错误

不要看到 `key` 有值就认为 SQL 一定快；不要忽略 `rows` 很大、`Using filesort`、`Using temporary` 和回表成本。

## 面试追问

- `type` 字段从好到差大致怎么理解？
- `key` 和 `possible_keys` 有什么区别？
- `Using filesort` 一定代表坏事吗？

## 排障/边界

普通 EXPLAIN 是估算，不是真实执行。估算和实际差异大时，用 `EXPLAIN ANALYZE` 或 optimizer trace 进一步确认。

## 相关主题

- [[MySQL/08-Performance-Diagnostics/EXPLAIN-ANALYZE|EXPLAIN ANALYZE]]
- [[MySQL/08-Performance-Diagnostics/optimizer-trace|optimizer trace]]
- [[MySQL/05-Indexing/Index-索引概念|Index 索引概念]]
