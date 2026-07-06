---
title: Row Lock 行锁
description: 行锁用于控制对具体记录的并发修改，InnoDB 基于索引记录加锁。
tags:
  - MySQL
  - Transaction
category: MySQL
---

# Row Lock 行锁

## 方法定位

行锁用于控制对具体记录的并发修改，InnoDB 基于索引记录加锁。

## 基本语法

```sql
UPDATE article SET status = 0 WHERE id = 10;
```

## 示例场景

编辑文章时，更新语句会锁住匹配到的记录，其他事务修改同一记录需要等待。

## 使用边界

适合高并发 OLTP；前提是条件能通过索引定位到行。

## 常见错误

不要让更新条件无法命中索引，否则可能扩大锁范围。

## 调优提示

为更新和删除条件建立合适索引，减少锁扫描范围。

## 相关主题

- [[MySQL/05-Indexing/Index-索引概念|Index 索引概念]]
- [[MySQL/06-Transaction-Lock/Deadlock-死锁|Deadlock 死锁]]


