---
title: 崩溃恢复 Crash Recovery
description: 说明 InnoDB 崩溃恢复中 redo、undo、binlog 和 checkpoint 的协作路径。
tags:
  - MySQL
  - InnoDB
  - Recovery
category: MySQL
---

# 崩溃恢复 Crash Recovery

## 速览

崩溃恢复的目标是让数据库回到一致状态：已提交事务保留，未提交事务回滚，复制和恢复日志状态一致。

## 核心机制

启动恢复时，InnoDB 从 checkpoint 后的 redo 开始重放，恢复已提交但未写入数据页的修改。未提交事务通过 undo 回滚。若事务处于 redo prepare 状态，还要结合 binlog 是否完整来判断提交还是回滚。

## SQL/配置示例

```sql
SHOW ENGINE INNODB STATUS;
SHOW VARIABLES LIKE 'innodb_fast_shutdown';
```

## 项目落地

异常宕机后不要直接删除日志文件。先保留现场、查看错误日志和恢复进度。生产要有备份恢复演练，避免把崩溃恢复当成唯一保护手段。

## 常见错误

不要把崩溃恢复等同于备份；它只能处理数据库自身一致性，不能恢复误删、误更新或业务逻辑错误。

## 面试追问

- 崩溃后 redo 和 undo 分别做什么？
- prepare 状态事务如何处理？
- 为什么 checkpoint 会影响恢复时间？

## 相关主题

- [redo log](redo-log.md)
- [undo log](undo-log.md)
- [binlog 与 redo log 协作](binlog-与-redo-log-协作.md)
- [备份恢复 Backup Restore](../10-Operations/备份恢复-Backup-Restore.md)
