---
title: 备份恢复 Backup Restore
description: 说明 MySQL 备份类型、恢复演练和为什么备份必须可验证。
tags:
  - MySQL
  - Operations
  - Backup
category: MySQL
---

# 备份恢复 Backup Restore

## 速览

备份不是“有文件就行”，而是要能在目标时间内恢复出正确数据。MySQL 常见方案包括逻辑备份、物理备份、全量备份加 binlog 时间点恢复。

## 核心机制

逻辑备份如 `mysqldump` 导出 SQL，跨版本和迁移友好，但大库恢复慢。物理备份复制数据文件，速度快但依赖工具、版本和存储一致性。时间点恢复通常依赖全量备份加 binlog 重放。

## SQL/配置示例

```bash
mysqldump --single-transaction --routines --triggers app_db > app_db.sql
mysql app_db < app_db.sql
```

## 项目落地

关键库要定义 RPO、RTO、备份频率、保留周期、加密、异地存储和恢复演练。误删恢复要先停写或隔离环境恢复，再做数据校验。

## 常见错误

不要把主从复制当备份；误删会被复制到副本。不要只做备份不做恢复演练。

## 面试追问

- 逻辑备份和物理备份区别是什么？
- 如何恢复到误删前某一时间点？
- 为什么复制不是备份？

## 相关主题

- [[MySQL/09-Replication-HA/binlog-Binary-Log|binlog Binary Log]]
- [[MySQL/07-InnoDB-Internals/崩溃恢复-Crash-Recovery|崩溃恢复 Crash Recovery]]

## 参考资料

- [MySQL 8.4 Reference Manual - mysqldump](https://dev.mysql.com/doc/refman/8.4/en/mysqldump.html)
