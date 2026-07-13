---
title: binlog 与 redo log 协作
description: 说明 MySQL Server 层 binlog 与 InnoDB redo log 如何通过两阶段提交保证一致性。
tags:
  - MySQL
  - InnoDB
  - binlog
  - redo-log
category: MySQL
---

# binlog 与 redo log 协作

## 速览

redo log 负责 InnoDB 崩溃恢复，binlog 负责复制和时间点恢复。事务提交时需要让两份日志保持一致，否则可能出现主库恢复状态和复制出去的状态不一致。

## 核心机制

MySQL 使用两阶段提交：先把 redo 写到 prepare 状态，再写 binlog，最后提交 redo。崩溃恢复时，如果 redo 处于 prepare，会检查对应 binlog 是否完整；完整则提交，不完整则回滚。这样能避免“redo 有、binlog 没有”或“binlog 有、redo 没有”的不一致。

## SQL/配置示例

```sql
SHOW VARIABLES LIKE 'log_bin';
SHOW VARIABLES LIKE 'sync_binlog';
SHOW VARIABLES LIKE 'innodb_flush_log_at_trx_commit';
```

## 项目落地

高可靠生产环境要同时关注 `sync_binlog` 和 `innodb_flush_log_at_trx_commit`。如果为了性能降低刷盘等级，要明确可接受的数据丢失窗口。

## 常见错误

不要用 redo 代替 binlog，也不要以为开启 binlog 就自动保证单机崩溃恢复；二者职责不同但提交时必须协作。

## 面试追问

- 为什么需要两阶段提交？
- redo prepare 后崩溃怎么办？
- `sync_binlog=1` 和 `innodb_flush_log_at_trx_commit=1` 各保证什么？

## 相关主题

- [redo log](redo-log.md)
- [binlog Binary Log](../09-Replication-HA/binlog-Binary-Log.md)
- [崩溃恢复 Crash Recovery](崩溃恢复-Crash-Recovery.md)
