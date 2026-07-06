---
title: undo log
description: 说明 undo log 如何支持事务回滚、MVCC 旧版本和长事务风险。
tags:
  - MySQL
  - InnoDB
  - undo-log
category: MySQL
---

# undo log

## 速览

undo log 记录数据被修改前的旧版本，用于事务回滚，也用于 MVCC 快照读构造历史版本。

## 核心机制

执行 UPDATE/DELETE 时，InnoDB 会把旧值写入 undo。事务回滚时按 undo 撤销变更；普通一致性读需要旧版本时，也可能沿 undo 版本链找到对当前 Read View 可见的数据。事务提交后，undo 不能立刻删除，必须等没有更老的 Read View 需要它。

## SQL/配置示例

```sql
SHOW ENGINE INNODB STATUS;
SELECT * FROM information_schema.innodb_trx;
```

## 项目落地

长事务会拖住 undo 清理，导致历史版本堆积、空间上涨、快照读变慢。分页导出、人工查询、事务内远程调用都可能制造长事务。

## 常见错误

不要以为只要没有锁等待就没有事务问题；长时间打开的只读事务也可能影响 purge 清理。

## 面试追问

- undo log 同时服务哪两类能力？
- MVCC 为什么依赖 undo？
- 长事务为什么危险？

## 排障/边界

发现 history list 长、undo 空间增长或 purge 跟不上时，要定位长事务和大批量更新任务，优先缩短事务边界。

## 相关主题

- [[MySQL/06-Transaction-Lock/MVCC|MVCC]]
- [[MySQL/06-Transaction-Lock/Transaction-事务|Transaction 事务]]
- [[MySQL/07-InnoDB-Internals/崩溃恢复-Crash-Recovery|崩溃恢复 Crash Recovery]]
