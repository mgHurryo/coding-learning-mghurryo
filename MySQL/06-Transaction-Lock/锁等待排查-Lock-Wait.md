---
title: 锁等待排查 Lock Wait
description: 说明 MySQL/InnoDB 锁等待的定位步骤、常用观测入口和处理策略。
tags:
  - MySQL
  - Transaction
  - Lock
category: MySQL
---

# 锁等待排查 Lock Wait

## 速览

锁等待是事务持有锁未释放，另一个事务请求冲突锁而被阻塞。排查目标不是立刻 kill，而是先找出阻塞链、SQL、事务时长和业务影响。

## 核心机制

InnoDB 行锁依赖索引记录、间隙和 next-key 范围。长事务、无索引更新、大范围扫描、DDL metadata lock 都可能放大等待。等待超时通常由 `innodb_lock_wait_timeout` 控制，但超时只是保护，不是根因修复。

## SQL/配置示例

```sql
SHOW PROCESSLIST;
SHOW ENGINE INNODB STATUS;
SELECT * FROM performance_schema.data_lock_waits;
SELECT * FROM performance_schema.data_locks;
```

## 项目落地

线上先确认阻塞会话是否还在执行业务、是否可重试、是否有批任务或人工 SQL。对于 Spring 事务，要检查方法边界是否过大、是否事务内调用外部接口、是否批量更新一次锁太多行。

## 常见错误

不要只 kill 被阻塞的会话；真正要找的是 blocker。不要把锁等待全归因于数据库，业务事务边界和 SQL 索引同样重要。

## 面试追问

- 如何定位谁阻塞了谁？
- 行锁为什么会退化成大范围锁等待？
- 事务里为什么不能做慢外部调用？

## 排障/边界

紧急恢复可以 kill blocker，但事后必须复盘 SQL、索引、事务边界和重试策略。若是 metadata lock，要查未提交事务和正在执行的 DDL。

## 相关主题

- [Deadlock 死锁](Deadlock-死锁.md)
- [Next-Key Lock](Next-Key-Lock.md)
- [慢 SQL 排查流程](../08-Performance-Diagnostics/慢-SQL-排查流程.md)
