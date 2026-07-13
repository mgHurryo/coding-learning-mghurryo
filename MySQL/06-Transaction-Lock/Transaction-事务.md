---
title: Transaction 事务
description: 事务用于把多条 SQL 组成一个原子操作单元，要么全部成功，要么全部回滚。
tags:
  - MySQL
  - Transaction
category: MySQL
---

# Transaction 事务

## 方法定位

事务用于把多条 SQL 组成一个原子操作单元，要么全部成功，要么全部回滚。

## 基本语法

```sql
START TRANSACTION;
UPDATE account SET balance = balance - 100 WHERE id = 1;
UPDATE account SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

## 示例场景

转账、下单扣库存、创建订单明细等场景需要事务保证一致性。

## 使用边界

适合需要多步一致性的写操作；单条 SQL 已具备语句级原子性。

## 常见错误

不要把外部 HTTP 调用、文件处理等慢操作放进数据库事务中。

## 调优提示

事务越短越好，尽快提交或回滚，减少锁等待和 undo 压力。

## 相关主题

- [ACID](ACID.md)
- [Isolation Level 隔离级别](Isolation-Level-隔离级别.md)


