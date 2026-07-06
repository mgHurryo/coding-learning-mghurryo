---
title: InnoDB 架构总览
description: 从内存、磁盘、后台线程和日志协作角度总览 InnoDB 的可靠性与性能机制。
tags:
  - MySQL
  - InnoDB
category: MySQL
---

# InnoDB 架构总览

## 速览

InnoDB 的核心可以理解为：Buffer Pool 缓存数据页，redo log 保证崩溃恢复，undo log 支撑回滚和 MVCC，后台线程负责刷脏页、合并变更和清理历史版本。

## 核心机制

查询或修改数据时，InnoDB 以页为单位把数据读入 Buffer Pool。修改先更新内存页并写 redo log，事务提交时按刷盘策略保证持久性。旧版本写入 undo log，供回滚和快照读使用。脏页稍后由 checkpoint 和刷盘线程落到数据文件。

## SQL/配置示例

```sql
SHOW ENGINE INNODB STATUS;
SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
SHOW VARIABLES LIKE 'innodb_flush_log_at_trx_commit';
```

## 项目落地

业务 SQL 的性能不仅取决于索引，还取决于工作集能否留在 Buffer Pool、写入量是否导致脏页压力、长事务是否拖住 undo 清理。

## 常见错误

不要把 MySQL 只理解成“表文件 + SQL 执行器”。很多线上问题来自日志、刷盘、缓存命中、长事务和后台清理之间的相互影响。

## 面试追问

- 一条 UPDATE 在 InnoDB 内部大致经历什么？
- redo log 和 undo log 分别解决什么问题？
- Buffer Pool 为什么会影响读写性能？

## 相关主题

- [[MySQL/07-InnoDB-Internals/Buffer-Pool|Buffer Pool]]
- [[MySQL/07-InnoDB-Internals/redo-log|redo log]]
- [[MySQL/07-InnoDB-Internals/undo-log|undo log]]
- [[MySQL/07-InnoDB-Internals/checkpoint|checkpoint]]
