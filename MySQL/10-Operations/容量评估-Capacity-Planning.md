---
title: 容量评估 Capacity Planning
description: 说明 MySQL 容量评估中数据量、索引、增长率、保留周期和性能余量的计算思路。
tags:
  - MySQL
  - Operations
category: MySQL
---

# 容量评估 Capacity Planning

## 速览

容量评估不只是磁盘够不够，还要看数据增长、索引膨胀、备份空间、Buffer Pool 工作集、写入峰值和恢复时间目标。

## 核心机制

表数据、二级索引、binlog、redo、undo、临时文件和备份都会占用空间。数据量增长还会影响索引层级、查询扫描范围、DDL 时间和恢复时间。

## SQL/配置示例

```sql
SELECT table_schema, table_name, data_length, index_length
FROM information_schema.tables
WHERE table_schema = 'app_db';
```

## 项目落地

设计表时就要定义保留周期、归档策略和增长预估。日志表、流水表、消息表要特别关注时间维度和清理机制。

## 常见错误

不要等磁盘满了才归档；不要只估算数据，不估算索引和备份；不要忽略 binlog 保留周期。

## 相关主题

- [[MySQL/11-Scaling-Architecture/冷热数据-Hot-Cold-Data|冷热数据 Hot Cold Data]]
- [[MySQL/10-Operations/备份恢复-Backup-Restore|备份恢复 Backup Restore]]
