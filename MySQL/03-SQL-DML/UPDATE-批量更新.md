---
title: UPDATE 批量更新
description: 用于一次修改多条记录，常见于状态迁移、数据修复和批处理。
tags:
  - MySQL
  - DML
category: MySQL
---

# UPDATE 批量更新

## 方法定位

用于一次修改多条记录，常见于状态迁移、数据修复和批处理。

## 基本语法

```sql
UPDATE article SET status = 0 WHERE category_id = 3 AND status = 1;
```

## 示例场景

下线某个分类下的全部文章时，可以按分类和状态批量更新。

## 使用边界

适合影响范围可控的批处理；大表批量更新应分批执行。

## 常见错误

不要用模糊条件直接更新生产数据；不要在一个事务里更新过多行。

## 调优提示

分批按主键范围更新，降低锁冲突、undo 膨胀和复制延迟风险。

## 相关主题

- [WHERE 调优](../08-Performance-Diagnostics/WHERE-调优.md)
- [Deadlock 死锁](../06-Transaction-Lock/Deadlock-死锁.md)


