---
title: GTID
description: 说明 Global Transaction Identifier 如何简化复制定位、故障切换和拓扑管理。
tags:
  - MySQL
  - Replication
  - GTID
category: MySQL
---

# GTID

## 速览

GTID 是全局事务标识，用来唯一标识复制拓扑中的事务。它让副本知道自己执行过哪些事务，简化自动定位、故障切换和补齐数据。

## 核心机制

开启 GTID 后，每个事务都有 source UUID 加事务序号。副本连接 source 时可以基于已执行 GTID 集合请求缺失事务，不再强依赖传统 binlog 文件名和 offset。

## SQL/配置示例

```sql
SHOW VARIABLES LIKE 'gtid_mode';
SHOW VARIABLES LIKE 'enforce_gtid_consistency';
SHOW REPLICA STATUS\G
```

## 项目落地

高可用和多副本环境优先使用 GTID，故障切换和新副本接入更清晰。上线前要确认应用 SQL 满足 GTID 一致性约束。

## 常见错误

不要在未理解 executed_gtid_set 和 purged_gtid_set 的情况下手工改复制状态；这可能造成数据缺口或重复应用。

## 面试追问

- GTID 相比 file/position 有什么优势？
- GTID 如何帮助 failover？
- 什么情况下 GTID 集合会出问题？

## 相关主题

- [[MySQL/09-Replication-HA/故障切换-Failover|故障切换 Failover]]
- [[MySQL/09-Replication-HA/主从复制-Replication|主从复制 Replication]]

## 参考资料

- [MySQL 8.4 Reference Manual - GTID Life Cycle](https://dev.mysql.com/doc/refman/8.4/en/replication-gtids-lifecycle.html)
