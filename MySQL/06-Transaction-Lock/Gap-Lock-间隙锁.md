---
title: Gap Lock 间隙锁
description: 间隙锁锁住索引记录之间的范围，用于防止特定隔离级别下的幻读。
tags:
  - MySQL
  - Transaction
category: MySQL
---

# Gap Lock 间隙锁

## 方法定位

间隙锁锁住索引记录之间的范围，用于防止特定隔离级别下的幻读。

## 基本语法

```sql
SELECT * FROM article WHERE id BETWEEN 10 AND 20 FOR UPDATE;
```

## 示例场景

范围锁定文章 ID 区间时，可能阻止其他事务在该区间插入新记录。

## 使用边界

常见于 InnoDB 可重复读下的范围当前读；普通快照读不会直接加间隙锁。

## 常见错误

不要忽略范围查询更新可能锁住不存在的间隙。

## 调优提示

缩小范围条件，使用唯一索引等值查询，可减少不必要的间隙锁影响。

## 相关主题

- [[MySQL/06-Transaction-Lock/Next-Key-Lock|Next-Key Lock]]
- [[MySQL/06-Transaction-Lock/SELECT-FOR-UPDATE|SELECT FOR UPDATE]]


