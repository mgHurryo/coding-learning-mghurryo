---
title: 数据迁移 Data Migration
description: 说明 MySQL 数据迁移、表结构变更、灰度校验和回滚设计。
tags:
  - MySQL
  - Operations
  - Migration
category: MySQL
---

# 数据迁移 Data Migration

## 速览

数据迁移包括表结构变更、历史数据修正、库表拆分和跨环境搬迁。核心是可回滚、可校验、可灰度、可观测。

## 核心机制

安全迁移通常分阶段：新增兼容字段或表；双写或回填；校验数量和校验和；灰度读新结构；切流；保留回滚窗口；最后清理旧结构。

## SQL/配置示例

```sql
SELECT COUNT(*) FROM old_table;
SELECT COUNT(*) FROM new_table;
```

## 项目落地

Spring Boot 项目迁移要配合版本发布。先让旧代码兼容新字段，再发布写入逻辑，最后迁移读取路径，避免代码和表结构不兼容。

## 常见错误

不要在一个大事务里迁移海量数据；不要没有校验就删旧字段；不要把回滚寄托在“重新跑一次脚本”。

## 相关主题

- [Online DDL](../02-Schema-DDL/Online-DDL.md)
- [分库分表 Sharding](../11-Scaling-Architecture/分库分表-Sharding.md)
