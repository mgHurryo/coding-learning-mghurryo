---
title: 参数配置 Server Variables
description: 说明 MySQL 参数配置的查看、动态修改、持久化和回滚注意事项。
tags:
  - MySQL
  - Operations
category: MySQL
---

# 参数配置 Server Variables

## 速览

MySQL 参数影响连接、内存、日志、优化器、复制和 InnoDB 行为。改参数前必须知道作用范围、是否动态、是否持久化和回滚方式。

## 核心机制

变量分为 global/session，有些可动态修改，有些需要重启。MySQL 支持 `SET PERSIST` 将变量持久化，但生产仍应纳入配置管理和变更审计。

## SQL/配置示例

```sql
SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
SET GLOBAL slow_query_log = ON;
SET PERSIST long_query_time = 1;
```

## 项目落地

参数变更要有基线、灰度、观测窗口和回滚命令。连接数、Buffer Pool、redo、binlog 刷盘等级这类参数不能照抄网上模板。

## 常见错误

不要只为了压测结果调大连接数；数据库承载能力由 CPU、内存、I/O、锁和 SQL 共同决定。

## 相关主题

- [Buffer Pool](Buffer-Pool.md)
- [redo log](redo-log.md)
- [Slow Query Log 慢查询日志](Slow-Query-Log-慢查询日志.md)
