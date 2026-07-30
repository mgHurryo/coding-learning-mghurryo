---
title: 主从复制 Replication
description: 说明 MySQL source/replica 复制链路、关键线程、延迟来源和项目使用边界。
tags:
  - MySQL
  - Replication
category: MySQL
---

# 主从复制 Replication

## 速览

MySQL 复制把 source 上的事务变更传到 replica，常用于读扩展、备份、灾备和高可用。复制提升可用性，但不等于强一致。

## 核心机制

source 写 binlog；replica receiver 线程拉取 binlog 写入 relay log；applier 线程读取 relay log 并应用。复制可以是异步、半同步或组复制等模式。传统异步复制下，source 提交成功不等待 replica 应用完成，因此可能出现复制延迟。

## SQL/配置示例

```sql
SHOW REPLICA STATUS\G
START REPLICA;
STOP REPLICA;
```

## 项目落地

读写分离时，刚写完立刻读副本可能读到旧数据。订单、支付、权限变更等强一致读应走主库或使用会话一致性策略。

## 常见错误

不要把 replica 当作永远实时；不要把复制当作备份的替代品；不要忽略大事务和 DDL 对复制延迟的影响。

## 面试追问

- MySQL 复制的基本流程是什么？
- 复制延迟有哪些来源？
- 为什么主从不等于强一致？

## 相关主题

- [binlog Binary Log](binlog-Binary-Log.md)
- [relay log](relay-log.md)
- [复制延迟 Replication Lag](复制延迟-Replication-Lag.md)

## 参考资料

- [MySQL 8.4 Reference Manual - Replication](https://dev.mysql.com/doc/refman/8.4/en/replication.html)
