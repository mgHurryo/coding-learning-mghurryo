---
title: redo log
description: 说明 InnoDB redo log 如何保证已提交事务在崩溃后可以恢复。
tags:
  - MySQL
  - InnoDB
  - redo-log
category: MySQL
---

# redo log

## 速览

redo log 是 InnoDB 的重做日志，用于保证持久性。事务修改数据页后，即使脏页还没刷到数据文件，只要 redo 按策略持久化，崩溃恢复时就能重放变更。

## 核心机制

InnoDB 更新页时先改 Buffer Pool，再写 redo log buffer。提交时根据 `innodb_flush_log_at_trx_commit` 决定是否每次提交都刷盘。崩溃后，InnoDB 从 checkpoint 位置开始扫描 redo，把已提交但未落盘的数据页恢复到一致状态。

## SQL/配置示例

```sql
SHOW VARIABLES LIKE 'innodb_flush_log_at_trx_commit';
SHOW VARIABLES LIKE 'innodb_redo_log_capacity';
```

## 项目落地

高可靠业务通常要求提交即持久，不能为了吞吐随意降低刷盘安全性。写入峰值大时，要关注 redo 容量、checkpoint 压力和磁盘 fsync 能力。

## 常见错误

不要把 redo log 和 binlog 混为一谈：redo 属于 InnoDB 崩溃恢复，binlog 属于 Server 层复制和时间点恢复。

## 面试追问

- redo log 为什么可以让数据页延迟刷盘？
- redo log 和 binlog 的区别是什么？
- `innodb_flush_log_at_trx_commit` 不同取值影响什么？

## 排障/边界

写入延迟周期性升高时，检查 redo checkpoint 是否追得太近、脏页刷盘是否跟不上、磁盘延迟是否异常。

## 相关主题

- [binlog 与 redo log 协作](binlog-与-redo-log-协作.md)
- [checkpoint](checkpoint.md)
- [崩溃恢复 Crash Recovery](崩溃恢复-Crash-Recovery.md)

## 参考资料

- [MySQL 8.4 Reference Manual - InnoDB Redo Log](https://dev.mysql.com/doc/refman/8.4/en/innodb-redo-log.html)
