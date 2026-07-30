---
title: Batch Insert 批量写入调优
description: 优化批量写入的批次大小、事务边界和索引维护成本。
tags:
  - MySQL
  - Performance
category: MySQL
---

# Batch Insert 批量写入调优

## 方法定位

优化批量写入的批次大小、事务边界和索引维护成本。

## 基本语法

```sql
INSERT INTO article_tag (article_id, tag_id) VALUES (1, 10), (1, 11), (1, 12);
```

## 示例场景

保存文章标签关系时，一次插入多条关联记录。

## 使用边界

适合批量导入和关系表写入；实时核心交易要优先保证一致性。

## 常见错误

不要单条循环提交；不要一次批量过大导致包大小或事务压力问题。

## 调优提示

控制批次大小，使用事务包裹合理批次，减少不必要索引和触发器成本。

## 相关主题

- [INSERT 批量插入](INSERT-批量插入.md)
- [MyBatis 批量操作](MyBatis-批量操作.md)


