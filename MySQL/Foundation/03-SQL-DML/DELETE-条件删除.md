---
title: DELETE 条件删除
description: 用于删除满足条件的记录，属于高风险写操作。
tags:
  - MySQL
  - DML
category: MySQL
---

# DELETE 条件删除

## 方法定位

用于删除满足条件的记录，属于高风险写操作。

## 基本语法

```sql
DELETE FROM article WHERE id = 10;
```

## 示例场景

删除用户草稿文章时，根据文章 ID 和用户 ID 双条件删除，避免越权。

## 使用边界

适合确实不再需要的数据；多数业务更适合逻辑删除。

## 常见错误

不要漏写 `WHERE`；不要用不确定条件删除大批生产数据。

## 调优提示

大批量删除建议按主键分批，避免长事务和大量锁等待。

## 相关主题

- [Soft Delete 逻辑删除](Soft-Delete-逻辑删除.md)
- [TRUNCATE 与 DELETE 区别](TRUNCATE-与-DELETE-区别.md)


