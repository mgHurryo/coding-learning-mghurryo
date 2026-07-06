---
title: relay log
description: 说明 replica 如何通过 relay log 保存并应用来自 source 的复制事件。
tags:
  - MySQL
  - Replication
category: MySQL
---

# relay log

## 速览

relay log 是 replica 本地保存 source binlog 事件的中继日志。复制线程先把事件写入 relay log，再由 applier 线程应用到本地数据。

## 核心机制

复制链路通常分成接收和应用两段：receiver 从 source 拉取 binlog 事件，写入 relay log；applier 读取 relay log 并执行。这样网络接收和本地执行可以解耦，也便于记录复制进度。

## SQL/配置示例

```sql
SHOW REPLICA STATUS\G
SHOW VARIABLES LIKE 'relay_log%';
```

## 项目落地

复制延迟可能发生在拉取阶段，也可能发生在应用阶段。看到 Seconds_Behind_Source 升高时，要区分网络、source 压力、replica SQL 应用慢还是大事务导致。

## 常见错误

不要手工删除 relay log 文件；应通过正常复制管理和配置处理。异常操作可能破坏复制状态。

## 相关主题

- [[MySQL/09-Replication-HA/主从复制-Replication|主从复制 Replication]]
- [[MySQL/09-Replication-HA/复制延迟-Replication-Lag|复制延迟 Replication Lag]]

## 参考资料

- [MySQL 8.4 Reference Manual - The Relay Log](https://dev.mysql.com/doc/refman/8.4/en/replica-logs-relaylog.html)
