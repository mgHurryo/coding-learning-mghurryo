---
title: INNER JOIN
description: 用于返回两张表中匹配成功的记录。
tags:
  - MySQL
  - Query
category: MySQL
---

# INNER JOIN

## 方法定位

用于返回两张表中匹配成功的记录。

## 基本语法

```sql
SELECT a.id, a.title, u.username FROM article a INNER JOIN user u ON a.user_id = u.id;
```

## 示例场景

查询文章列表时同时展示作者用户名。

## 使用边界

适合只需要有关联记录的数据；无匹配记录会被过滤掉。

## 常见错误

不要忘记 `ON` 条件；不要把连接条件写错导致笛卡尔积。

## 调优提示

连接字段两侧应有合适索引，小结果集驱动大表通常更稳定。

## 相关主题

- [JOIN 调优](../08-Performance-Diagnostics/JOIN-调优.md)
- [LEFT JOIN](LEFT-JOIN.md)


