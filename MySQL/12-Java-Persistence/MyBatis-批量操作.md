---
title: MyBatis 批量操作
description: 说明 MyBatis 中批量插入、批量更新、foreach、ExecutorType.BATCH 和事务边界。
tags:
  - MySQL
  - MyBatis
  - Batch
category: MySQL
---

# MyBatis 批量操作

## 速览

MyBatis 批量操作用于减少网络往返和 SQL 执行次数，但必须控制批量大小、事务边界、锁范围和失败重试策略。

## 核心机制

常见方式包括 XML `<foreach>` 拼接多值 INSERT、循环调用 Mapper 配合 `ExecutorType.BATCH`、或使用数据库原生批量 SQL。批量越大，单次事务持锁越久，undo、redo、binlog 压力越大，失败回滚成本也越高。

## SQL/配置示例

```xml
<insert id="batchInsert">
  INSERT INTO article(title, user_id)
  VALUES
  <foreach collection="list" item="item" separator=",">
    (#{item.title}, #{item.userId})
  </foreach>
</insert>
```

## 项目落地

导入、同步、补偿任务应分批执行，例如每 500 或 1000 条一批，并记录进度。接口实时请求不要一次提交过大的批量写入。

## 常见错误

不要把几万条数据拼成一条超大 SQL；不要在一个事务里批量更新过多行；不要忽略唯一键冲突和部分失败处理。

## 面试追问

- MyBatis foreach 批量和 BATCH executor 有什么区别？
- 批量写入为什么要控制大小？
- 批量更新如何降低锁冲突？

## 排障/边界

批量任务慢时，检查单批大小、索引维护成本、锁等待、redo/binlog 写入压力和连接池占用。失败后要能从上次成功位置继续。

## 相关主题

- [Batch Insert 批量写入调优](../08-Performance-Diagnostics/Batch-Insert-批量写入调优.md)
- [INSERT 批量插入](../03-SQL-DML/INSERT-批量插入.md)
- [Deadlock 死锁](../06-Transaction-Lock/Deadlock-死锁.md)
