---
title: Partition 分区表
description: 说明 MySQL 分区表的定位、常见分区方式和与分库分表的区别。
tags:
  - MySQL
  - Architecture
  - Partition
category: MySQL
---

# Partition 分区表

## 速览

分区表是在单张逻辑表内部按规则拆成多个物理分区。它适合按时间或范围管理大表数据，但不是分库分表的完全替代品。

## 核心机制

常见分区方式有 RANGE、LIST、HASH、KEY。查询条件包含分区键时，优化器可以做 partition pruning，只扫描相关分区。归档或删除历史数据时，可以按分区快速处理。

## SQL/配置示例

```sql
CREATE TABLE log_event (
  id BIGINT NOT NULL,
  created_at DATE NOT NULL
)
PARTITION BY RANGE COLUMNS(created_at) (
  PARTITION p202607 VALUES LESS THAN ('2026-08-01')
);
```

## 项目落地

日志、流水、历史记录适合按时间分区。高并发核心交易表使用分区前要谨慎评估索引、唯一键和查询模式限制。

## 常见错误

不要指望分区自动提升所有查询；没有分区键的查询仍可能扫描多个分区。

## 相关主题

- [冷热数据 Hot Cold Data](冷热数据-Hot-Cold-Data.md)
- [容量评估 Capacity Planning](容量评估-Capacity-Planning.md)
