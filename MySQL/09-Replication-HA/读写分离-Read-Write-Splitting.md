---
title: 读写分离 Read Write Splitting
description: 说明读写分离的收益、旧读风险和后端项目中的一致性处理。
tags:
  - MySQL
  - Replication
  - Architecture
category: MySQL
---

# 读写分离 Read Write Splitting

## 速览

读写分离把写请求发到主库，把部分读请求发到副本，用于扩展读能力。它的最大代价是复制延迟带来的读一致性问题。

## 核心机制

主库提交后，副本要经过拉取、写 relay log、应用事务等步骤才能看到最新数据。异步复制下这个过程没有强同步保证，所以写后立即读副本可能读到旧值。

## SQL/配置示例

```text
write -> source
read eventual-consistency -> replica
read after write / strong consistency -> source
```

## 项目落地

用户提交后立即展示最新状态、权限校验、支付订单状态等读主库；列表浏览、统计看板、推荐结果等可读副本。中间件或数据源路由要支持强制读主。

## 常见错误

不要把所有 SELECT 都路由到副本；不要忽略事务内读写一致性；不要在延迟不可控时承诺实时查询。

## 相关主题

- [复制延迟 Replication Lag](复制延迟-Replication-Lag.md)
- [DataSource 数据源](../12-Java-Persistence/DataSource-数据源.md)
- [数据库边界与业务幂等](../11-Scaling-Architecture/数据库边界与业务幂等.md)
