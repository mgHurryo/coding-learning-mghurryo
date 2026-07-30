---
title: 监控指标 Monitoring Metrics
description: 说明 MySQL 监控中连接、吞吐、缓存、锁、慢 SQL、复制和磁盘指标的分层观察。
tags:
  - MySQL
  - Operations
  - Monitoring
category: MySQL
---

# 监控指标 Monitoring Metrics

## 速览

MySQL 监控要覆盖连接、吞吐、延迟、缓存、锁、事务、复制、磁盘和错误日志。单一指标很少能解释问题，关键是把指标和业务现象关联。

## 核心机制

常见入口包括 `SHOW GLOBAL STATUS`、Performance Schema、慢查询日志、错误日志和系统层磁盘/CPU/网络指标。读慢可能是 Buffer Pool miss、索引失效、锁等待或磁盘抖动；写慢可能是 redo、刷脏页、binlog fsync 或复制压力。

## SQL/配置示例

```sql
SHOW GLOBAL STATUS LIKE 'Threads_connected';
SHOW GLOBAL STATUS LIKE 'Innodb_buffer_pool_read%';
SHOW GLOBAL STATUS LIKE 'Innodb_row_lock%';
```

## 项目落地

仪表盘至少要有 QPS/TPS、连接数、慢 SQL 数、Buffer Pool 命中、锁等待、磁盘延迟、复制延迟和错误日志告警。

## 常见错误

不要只监控 CPU；数据库瓶颈常常是 I/O、锁、连接池或 SQL 计划变化。不要没有基线就设置告警阈值。

## 相关主题

- [慢 SQL 排查流程](慢-SQL-排查流程.md)
- [锁等待排查 Lock Wait](锁等待排查-Lock-Wait.md)
- [复制延迟 Replication Lag](复制延迟-Replication-Lag.md)
