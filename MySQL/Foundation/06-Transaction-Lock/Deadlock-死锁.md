---
title: Deadlock 死锁
description: 说明 InnoDB 死锁的成因、检测、日志分析和业务重试策略。
tags:
  - MySQL
  - Transaction
  - Lock
category: MySQL
---

# Deadlock 死锁

## 速览

死锁是多个事务互相等待对方持有的锁，形成循环依赖。InnoDB 会检测死锁并回滚其中一个事务，让其它事务继续执行。

## 核心机制

常见成因包括不同事务按不同顺序更新资源、范围条件加锁重叠、缺索引导致扫描和加锁范围变大、批量更新顺序不稳定。死锁不是数据库坏了，而是并发写入设计需要调整。

## SQL/配置示例

```sql
SHOW ENGINE INNODB STATUS\G
SHOW VARIABLES LIKE 'innodb_print_all_deadlocks';
```

## 项目落地

业务代码必须能识别死锁异常并做有限次数重试。更新多条资源时尽量按固定顺序处理，例如按主键升序；批量任务分批提交，降低锁持有时间。

## 常见错误

不要认为死锁完全可以避免；高并发系统更重要的是降低概率、缩短事务、完善重试和监控。

## 面试追问

- 死锁和锁等待有什么区别？
- 如何从死锁日志看出冲突 SQL？
- 为什么固定更新顺序能降低死锁？

## 排障/边界

先保留 `SHOW ENGINE INNODB STATUS` 中 latest detected deadlock，再定位事务 SQL、索引和业务调用路径。不要只重试而不修复高频死锁根因。

## 相关主题

- [锁等待排查 Lock Wait](锁等待排查-Lock-Wait.md)
- [Row Lock 行锁](Row-Lock-行锁.md)
- [WHERE 调优](WHERE-调优.md)
