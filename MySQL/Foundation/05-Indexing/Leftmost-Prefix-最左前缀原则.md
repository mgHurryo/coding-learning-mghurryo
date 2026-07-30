---
title: Leftmost Prefix 最左前缀原则
description: 说明联合索引从最左列开始连续匹配才能被充分利用。
tags:
  - MySQL
  - Index
category: MySQL
---

# Leftmost Prefix 最左前缀原则

## 方法定位

说明联合索引从最左列开始连续匹配才能被充分利用。

## 基本语法

```sql
-- 索引 (user_id, status, create_time) 可匹配 user_id 或 user_id + status
```

## 示例场景

`WHERE user_id = 1 AND status = 1` 能使用上述联合索引前两列。

## 使用边界

适合判断联合索引是否能服务某条 SQL；不是唯一规则，还要看优化器成本。

## 常见错误

不要跳过最左列直接期待后续列发挥完整索引效果。

## 调优提示

把高频等值过滤字段放在联合索引左侧，范围字段之后的列利用会受限。

## 相关主题

- [Composite Index 联合索引](Composite-Index-联合索引.md)
- [BETWEEN 范围查询](BETWEEN-范围查询.md)


