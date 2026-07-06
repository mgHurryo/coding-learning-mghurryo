---
title: 半同步复制 Semisynchronous Replication
description: 说明半同步复制如何在提交返回前等待至少一个副本确认接收日志。
tags:
  - MySQL
  - Replication
category: MySQL
---

# 半同步复制 Semisynchronous Replication

## 速览

半同步复制让 source 在事务提交返回客户端前，等待至少一个 replica 确认已接收并记录事务事件。它降低数据丢失风险，但不保证副本已经应用完成。

## 核心机制

replica 接收事务事件并写入 relay log 后返回确认。若超时没有副本确认，source 会退回异步模式；当副本追上后可恢复半同步。它在可靠性和延迟之间做折中。

## SQL/配置示例

```sql
SHOW VARIABLES LIKE 'rpl_semi_sync%';
SHOW STATUS LIKE 'Rpl_semi_sync%';
```

## 项目落地

适合对主库宕机数据丢失敏感、又能接受提交延迟增加的业务。仍需结合 GTID、监控和 failover 流程。

## 常见错误

不要以为半同步代表强一致；确认的是接收和记录日志，不是业务查询一定能在副本读到最新数据。

## 相关主题

- [[MySQL/09-Replication-HA/主从复制-Replication|主从复制 Replication]]
- [[MySQL/09-Replication-HA/故障切换-Failover|故障切换 Failover]]

## 参考资料

- [MySQL 8.4 Reference Manual - Semisynchronous Replication](https://dev.mysql.com/doc/refman/8.4/en/replication-semisync.html)
