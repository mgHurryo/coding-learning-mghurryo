---
title: 复制延迟 Replication Lag
description: 说明 MySQL 复制延迟的来源、观测指标和业务缓解策略。
tags:
  - MySQL
  - Replication
category: MySQL
---

# 复制延迟 Replication Lag

## 速览

复制延迟是 replica 落后 source 的现象。它会导致读写分离下读到旧数据，也会影响故障切换时的数据完整性判断。

## 核心机制

延迟可能来自 source 大事务、网络传输、replica 单线程或并行应用不足、缺少索引导致应用慢、DDL 阻塞、磁盘 I/O 压力等。`Seconds_Behind_Source` 有参考价值，但不能单独作为真相。

## SQL/配置示例

```sql
SHOW REPLICA STATUS\G
SHOW PROCESSLIST;
```

## 项目落地

强一致读走主库；可容忍旧数据的列表、统计、推荐可读副本。写后读场景要使用主库读、延迟检测、会话粘滞或业务等待策略。

## 常见错误

不要只看一个延迟指标；要结合 relay log 积压、applier 状态、事务大小和业务 SQL。

## 面试追问

- 复制延迟有哪些常见原因？
- 如何缓解读写分离中的旧读？
- 大事务为什么会造成延迟？

## 相关主题

- [[MySQL/09-Replication-HA/读写分离-Read-Write-Splitting|读写分离 Read Write Splitting]]
- [[MySQL/09-Replication-HA/relay-log|relay log]]
