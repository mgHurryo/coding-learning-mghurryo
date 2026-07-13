---
title: checkpoint
description: 说明 InnoDB checkpoint 如何推进脏页刷盘和崩溃恢复起点。
tags:
  - MySQL
  - InnoDB
category: MySQL
---

# checkpoint

## 速览

checkpoint 是 InnoDB 控制脏页刷盘和崩溃恢复起点的机制。它让 redo log 不必无限增长，也让恢复只需从最近的安全位置开始。

## 核心机制

更新产生 redo，同时数据页变成脏页。后台线程逐步刷脏页，checkpoint 向前推进，表示某些 redo 之前的变更已经反映到数据页。若 checkpoint 追不上写入速度，redo 空间压力会迫使更多刷盘，写入延迟可能抖动。

## SQL/配置示例

```sql
SHOW ENGINE INNODB STATUS;
SHOW VARIABLES LIKE 'innodb_redo_log_capacity';
```

## 项目落地

写入高峰、批量导入、索引维护都可能制造脏页和 redo 压力。容量规划要看磁盘吞吐、redo 容量和业务可接受延迟。

## 常见错误

不要只调大 redo 容量而不看磁盘刷盘能力；容量只能缓冲压力，不能消除长期写入过载。

## 相关主题

- [redo log](redo-log.md)
- [Buffer Pool](Buffer-Pool.md)
