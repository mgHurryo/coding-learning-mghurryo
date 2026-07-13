---
title: 故障切换 Failover
description: 说明 MySQL 主库故障时提升副本、校验数据和恢复复制拓扑的核心步骤。
tags:
  - MySQL
  - Replication
  - HA
category: MySQL
---

# 故障切换 Failover

## 速览

Failover 是 source 故障后选择合适 replica 提升为新 source，并让应用流量和其它副本切换过去。核心风险是数据缺失、脑裂和重复写入。

## 核心机制

切换前要判断旧主是否真的不可写、各副本 GTID 或 binlog 位点是否追平、候选副本是否数据最新。切换后要更新应用连接、重建其它副本的复制来源，并保留旧主现场。

## SQL/配置示例

```sql
SHOW REPLICA STATUS\G
SHOW MASTER STATUS;
SHOW VARIABLES LIKE 'gtid_mode';
```

## 项目落地

生产应依赖成熟高可用组件或标准化 runbook，而不是临场手工拼命令。业务侧要设置合理连接超时和重试，避免故障窗口内写入放大。

## 常见错误

不要在旧主可能仍可写时同时提升新主；不要忽略未复制事务；不要在没有校验的情况下把旧主直接加回集群。

## 相关主题

- [GTID](GTID.md)
- [主从复制 Replication](主从复制-Replication.md)
- [备份恢复 Backup Restore](../10-Operations/备份恢复-Backup-Restore.md)
