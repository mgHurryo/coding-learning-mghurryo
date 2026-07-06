---
title: Next-Key Lock
description: Next-Key Lock 是记录锁和间隙锁的组合，用于锁住记录及其前后范围。
tags:
  - MySQL
  - Transaction
category: MySQL
---

# Next-Key Lock

## 方法定位

Next-Key Lock 是记录锁和间隙锁的组合，用于锁住记录及其前后范围。

## 基本语法

```sql
SELECT * FROM article WHERE category_id = 3 FOR UPDATE;
```

## 示例场景

按非唯一索引范围锁定文章时，可能产生 next-key lock，影响相邻范围插入。

## 使用边界

适合理解范围当前读和幻读防护；唯一索引等值命中时锁范围更小。

## 常见错误

不要只看 SQL 条件，还要结合执行计划和实际使用的索引判断锁范围。

## 调优提示

让锁定查询尽量走唯一索引或高选择性索引，避免扩大 next-key lock 范围。

## 相关主题

- [[MySQL/06-Transaction-Lock/Gap-Lock-间隙锁|Gap Lock 间隙锁]]
- [[MySQL/08-Performance-Diagnostics/EXPLAIN-key-字段|EXPLAIN key 字段]]


