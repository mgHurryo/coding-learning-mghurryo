---
title: EXPLAIN key 字段
description: `key` 表示优化器实际选择使用的索引。
tags:
  - MySQL
  - Performance
category: MySQL
---

# EXPLAIN key 字段

## 方法定位

`key` 表示优化器实际选择使用的索引。

## 基本语法

```sql
EXPLAIN SELECT id FROM article WHERE user_id = 1;
```

## 示例场景

即使 `possible_keys` 有多个索引，`key` 才是实际采用的索引。

## 使用边界

适合验证索引是否被使用；还要看 key_len 和 rows 判断使用程度。

## 常见错误

不要只建索引不验证；不要以为索引名出现就一定覆盖全部条件。

## 调优提示

如果 key 不是预期索引，检查统计信息、条件选择性、字段顺序和隐式转换。

## 相关主题

- [SHOW INDEX 查看索引](../05-Indexing/SHOW-INDEX-查看索引.md)
- [Composite Index 联合索引](../05-Indexing/Composite-Index-联合索引.md)


