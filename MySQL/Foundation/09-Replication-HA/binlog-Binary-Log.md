---
title: binlog Binary Log
description: 说明 MySQL binary log 在复制、时间点恢复和审计中的作用。
tags:
  - MySQL
  - Replication
  - binlog
category: MySQL
---

# binlog Binary Log

## 速览

binlog 是 MySQL Server 层记录数据变更的日志，主要用于复制和时间点恢复。它不同于 InnoDB redo log，后者服务于崩溃恢复。

## 核心机制

事务提交时，MySQL 把变更事件写入 binlog。replica 从 source 拉取 binlog，写入 relay log 后再应用。binlog 常见格式包括 statement、row 和 mixed，生产复制通常更关注 row 格式的确定性。

## SQL/配置示例

```sql
SHOW VARIABLES LIKE 'log_bin';
SHOW BINARY LOGS;
SHOW MASTER STATUS;
```

## 项目落地

开启 binlog 是主从复制、CDC、误操作按时间点恢复的基础。保留周期要结合备份周期和恢复目标设计，不能只按磁盘空间随意清理。

## 常见错误

不要把 binlog 当成单机崩溃恢复日志；不要清理还没被副本消费或恢复链路需要的 binlog。

## 面试追问

- binlog 和 redo log 有什么区别？
- row 格式和 statement 格式差异是什么？
- 为什么时间点恢复需要全量备份加 binlog？

## 相关主题

- [binlog 与 redo log 协作](binlog-与-redo-log-协作.md)
- [主从复制 Replication](主从复制-Replication.md)
- [备份恢复 Backup Restore](备份恢复-Backup-Restore.md)

## 参考资料

- [MySQL 8.4 Reference Manual - The Binary Log](https://dev.mysql.com/doc/refman/8.4/en/binary-log.html)
