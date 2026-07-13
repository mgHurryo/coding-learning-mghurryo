---
title: ACID
description: 说明事务的原子性、一致性、隔离性、持久性，以及它们在 InnoDB 中分别依赖哪些机制实现。
tags:
  - MySQL
  - Transaction
category: MySQL
---

# ACID

## 速览

ACID 是事务可靠性的四个目标：Atomicity 原子性、Consistency 一致性、Isolation 隔离性、Durability 持久性。它不是一条 SQL，而是一组数据库和业务共同维护的约束。

## 核心机制

原子性依赖 undo log 和事务回滚，保证事务内操作要么都生效，要么都撤销。持久性依赖 redo log 和刷盘策略，保证提交后的变更在崩溃后可恢复。隔离性依赖锁、MVCC 和隔离级别，控制并发事务互相可见的程度。一致性是结果目标，既依赖数据库约束，也依赖业务规则。

## SQL/配置示例

```sql
START TRANSACTION;
UPDATE account SET balance = balance - 100 WHERE id = 1;
UPDATE account SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

## 项目落地

转账、下单扣库存、支付状态流转等场景需要事务包住同库内的关键写入。跨服务操作不能只靠本地 ACID，要结合消息、幂等和补偿。

## 常见错误

不要以为用了事务就自动保证业务一致性；没有唯一约束、状态判断或幂等键，重复请求仍可能制造脏数据。

## 面试追问

- ACID 四个性质分别靠什么机制实现？
- 数据库一致性和业务一致性有什么区别？
- 为什么跨服务事务不能简单依赖 MySQL 本地事务？

## 排障/边界

出现“事务提交了但业务不一致”时，检查事务边界、异常回滚、唯一约束、并发状态更新和外部系统调用顺序。

## 相关主题

- [Transaction 事务](Transaction-事务.md)
- [undo log](../07-InnoDB-Internals/undo-log.md)
- [redo log](../07-InnoDB-Internals/redo-log.md)
- [数据库边界与业务幂等](../11-Scaling-Architecture/数据库边界与业务幂等.md)
