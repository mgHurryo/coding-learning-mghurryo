---
title: LIMIT 深分页调优
description: 说明 OFFSET 深分页为什么慢，以及 seek pagination、覆盖索引和延迟关联的优化方法。
tags:
  - MySQL
  - Performance
  - Pagination
category: MySQL
---

# LIMIT 深分页调优

## 速览

`LIMIT offset, size` 的 offset 很大时，MySQL 仍要扫描并丢弃前面大量记录。深分页优化重点是避免大 offset 扫描。

## 核心机制

`LIMIT 100000, 20` 并不是直接跳到第 100001 行，而是按执行计划找到前 100020 行，再丢弃前 100000 行。若还伴随排序和回表，成本会更高。

## SQL/配置示例

```sql
-- 深分页风险
SELECT id, title FROM article ORDER BY id LIMIT 100000, 20;

-- seek pagination
SELECT id, title FROM article
WHERE id > 100000
ORDER BY id
LIMIT 20;
```

## 项目落地

无限滚动、下一页游标、按时间或 id 翻页，比任意页码跳转更适合大数据列表。后台管理若必须跳页，可先用覆盖索引定位 id，再延迟关联查详情。

## 常见错误

不要给用户开放无限大的页码；不要用深分页导出全量数据；导出应走批处理游标和异步任务。

## 面试追问

- 深分页为什么慢？
- seek pagination 的限制是什么？
- 延迟关联如何减少回表？

## 排障/边界

若排序字段不唯一，seek pagination 要加上稳定 tie-breaker，例如 `(create_time, id)`，避免漏数据或重复数据。

## 相关主题

- [LIMIT 分页](LIMIT-分页.md)
- [Covering Index 覆盖索引](Covering-Index-覆盖索引.md)
- [ORDER BY 调优](ORDER-BY-调优.md)
