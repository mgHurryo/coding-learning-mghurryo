---
title: 冷热数据 Hot Cold Data
description: 说明按访问频率和时间价值拆分 MySQL 热数据、冷数据和归档数据。
tags:
  - MySQL
  - Architecture
category: MySQL
---

# 冷热数据 Hot Cold Data

## 速览

冷热数据拆分是把高频访问的近期数据留在主路径，把低频历史数据归档到低成本存储或历史表，降低核心表容量和索引压力。

## 核心机制

数据通常随时间变冷。订单、日志、消息、审计记录可以按业务保留周期分层：在线热表、近线历史表、离线归档。查询入口要明确默认查热数据，历史查询走专门路径。

## SQL/配置示例

```sql
INSERT INTO order_archive SELECT * FROM orders WHERE created_at < '2025-01-01';
DELETE FROM orders WHERE created_at < '2025-01-01' LIMIT 1000;
```

## 项目落地

归档要分批、可重试、可校验。产品上要区分近期查询和历史查询，不要让所有请求默认扫全量历史。

## 常见错误

不要用一次大 DELETE 清理多年数据；不要归档后没有校验和恢复路径。

## 相关主题

- [[MySQL/10-Operations/容量评估-Capacity-Planning|容量评估 Capacity Planning]]
- [[MySQL/11-Scaling-Architecture/Partition-分区表|Partition 分区表]]
