---
title: doublewrite buffer
description: 说明 InnoDB doublewrite buffer 如何降低数据页半写导致的损坏风险。
tags:
  - MySQL
  - InnoDB
category: MySQL
---

# doublewrite buffer

## 速览

doublewrite buffer 用于防止数据页写入过程中发生 partial page write。它让 InnoDB 在把页写到最终位置前，先写一份可恢复副本。

## 核心机制

数据页通常大于底层存储原子写粒度。若写到一半宕机，页可能损坏。doublewrite 先把页写到共享区域并刷盘，再写到真实表空间。恢复时如果发现页损坏，可以从 doublewrite 副本恢复。

## SQL/配置示例

```sql
SHOW VARIABLES LIKE 'innodb_doublewrite';
```

## 项目落地

默认开启更安全。只有在明确底层存储提供可靠原子写、并经过压测和风险评估后，才考虑调整。

## 常见错误

不要把 doublewrite 当成备份；它只保护页写入撕裂，不解决误删、逻辑错误或磁盘整体损坏。

## 相关主题

- [崩溃恢复 Crash Recovery](崩溃恢复-Crash-Recovery.md)
- [备份恢复 Backup Restore](备份恢复-Backup-Restore.md)
