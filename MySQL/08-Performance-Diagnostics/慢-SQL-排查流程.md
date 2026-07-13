---
title: 慢 SQL 排查流程
description: 从现象定位、执行计划、索引设计、SQL 改写到验证回归的慢 SQL 排查步骤。
tags:
  - MySQL
  - Performance
  - Slow-SQL
category: MySQL
---

# 慢 SQL 排查流程

## 速览

慢 SQL 排查要从真实现象出发：先定位 SQL 和耗时，再看执行计划和数据分布，最后用小步改动验证收益。不要一上来就盲目加索引。

## 核心机制

标准路径是：慢查询日志或 APM 找到 SQL；确认参数、频率、返回行数和业务入口；用 `EXPLAIN` 看访问方式、索引、估算行数和 Extra；必要时用 `EXPLAIN ANALYZE` 验证真实行数；再决定索引、SQL 改写、分页方式、缓存或业务拆分。

## SQL/配置示例

```sql
EXPLAIN SELECT id, title FROM article
WHERE user_id = 1 AND status = 1
ORDER BY create_time DESC
LIMIT 20;
```

## 项目落地

Spring Boot 项目要把慢 SQL 和接口、Mapper 方法、参数样本关联起来。调优后要回归常见参数、极端参数和并发场景，避免只优化一个样本。

## 常见错误

不要只看平均耗时；P95/P99 和偶发慢请求更接近用户体验。不要只看 SQL，不看锁等待、连接池等待、网络和应用层序列化。

## 面试追问

- 线上发现接口慢，你如何定位是不是 SQL 问题？
- `Using filesort` 一定要消除吗？
- 为什么加索引可能让写入变慢？

## 排障/边界

若慢 SQL 同时伴随锁等待，先处理 blocker；若是深分页或大结果集，要从产品交互和数据访问模式一起改。

## 相关主题

- [Slow Query Log 慢查询日志](Slow-Query-Log-慢查询日志.md)
- [EXPLAIN 使用方法](EXPLAIN-使用方法.md)
- [EXPLAIN ANALYZE](EXPLAIN-ANALYZE.md)
- [锁等待排查 Lock Wait](../06-Transaction-Lock/锁等待排查-Lock-Wait.md)
