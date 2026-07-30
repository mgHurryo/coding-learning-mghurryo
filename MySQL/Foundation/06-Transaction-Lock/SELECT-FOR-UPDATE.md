---
title: SELECT FOR UPDATE
description: 用于在事务中读取并锁定记录，后续通常会进行更新。
tags:
  - MySQL
  - Transaction
category: MySQL
---

# SELECT FOR UPDATE

## 方法定位

用于在事务中读取并锁定记录，后续通常会进行更新。

## 基本语法

```sql
START TRANSACTION;
SELECT * FROM article WHERE id = 10 FOR UPDATE;
UPDATE article SET status = 1 WHERE id = 10;
COMMIT;
```

## 示例场景

审核文章时先锁住文章记录，确认状态后再更新，避免并发重复审核。

## 使用边界

必须在事务中使用才有意义；只读展示接口不应使用。

## 常见错误

不要在事务外以为 `FOR UPDATE` 能长期锁定；不要锁住记录后执行慢业务逻辑。

## 调优提示

锁定条件必须命中高选择性索引，避免锁范围被放大。

## 相关主题

- [Transaction 事务](Transaction-事务.md)
- [Row Lock 行锁](Row-Lock-行锁.md)


