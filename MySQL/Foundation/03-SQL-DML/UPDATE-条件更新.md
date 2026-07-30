---
title: UPDATE 条件更新
description: 用于修改满足条件的记录，是业务状态变更的基础写法。
tags:
  - MySQL
  - DML
category: MySQL
---

# UPDATE 条件更新

## 方法定位

用于修改满足条件的记录，是业务状态变更的基础写法。

## 基本语法

```sql
UPDATE article SET title = "new title", update_time = NOW() WHERE id = 10;
```

## 示例场景

用户编辑文章标题时，根据文章 ID 更新标题和更新时间。

## 使用边界

适合明确定位记录的更新；批量修复数据要先备份并确认条件。

## 常见错误

不要漏写 `WHERE`；不要用非索引条件更新大表大量行。

## 调优提示

更新条件应尽量命中索引，事务内只保留必要逻辑，减少锁持有时间。

## 相关主题

- [UPDATE 批量更新](UPDATE-批量更新.md)
- [Row Lock 行锁](Row-Lock-行锁.md)


