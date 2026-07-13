---
title: Isolation Level 隔离级别
description: 说明 MySQL InnoDB 四种隔离级别、并发现象和默认 REPEATABLE READ 的实践含义。
tags:
  - MySQL
  - Transaction
category: MySQL
---

# Isolation Level 隔离级别

## 速览

隔离级别决定并发事务之间能看到什么。InnoDB 支持 READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ、SERIALIZABLE，默认是 REPEATABLE READ。

## 核心机制

RU 可能读到未提交数据；RC 每条语句看到提交后的新快照；RR 在同一事务内保持一致性读视图，并通过 next-key lock 等机制降低幻读影响；SERIALIZABLE 最严格，通常会牺牲并发。

## SQL/配置示例

```sql
SELECT @@transaction_isolation;
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
START TRANSACTION;
```

## 项目落地

大多数业务使用默认 RR 即可。若系统更关注减少锁冲突、并且能接受 RC 语义，可评估 RC，但必须回归并发状态流转、范围更新和一致性读行为。

## 常见错误

不要只背脏读、不可重复读、幻读，还要知道 MySQL InnoDB 的 MVCC 和锁会影响实际表现。不要随意全局修改隔离级别。

## 面试追问

- RC 和 RR 的 Read View 何时生成？
- InnoDB RR 如何处理幻读问题？
- 隔离级别越高为什么并发越差？

## 排障/边界

并发问题要结合隔离级别、SQL 类型、索引和执行计划判断。同一条 SQL 是否加范围锁，常常取决于索引是否命中。

## 相关主题

- [MVCC](MVCC.md)
- [Next-Key Lock](Next-Key-Lock.md)
- [当前读与快照读](../99-Common-Concepts/当前读与快照读.md)

## 参考资料

- [MySQL 8.4 Reference Manual - Transaction Isolation Levels](https://dev.mysql.com/doc/refman/8.4/en/innodb-transaction-isolation-levels.html)
