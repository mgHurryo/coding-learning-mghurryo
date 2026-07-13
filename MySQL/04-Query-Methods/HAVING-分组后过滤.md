---
title: HAVING 分组后过滤
description: 用于对分组聚合后的结果进行过滤。
tags:
  - MySQL
  - Query
category: MySQL
---

# HAVING 分组后过滤

## 方法定位

用于对分组聚合后的结果进行过滤。

## 基本语法

```sql
SELECT user_id, COUNT(*) AS total FROM article GROUP BY user_id HAVING total > 10;
```

## 示例场景

找出文章数量超过 10 篇的作者。

## 使用边界

适合过滤聚合结果；普通行过滤应放在 `WHERE`。

## 常见错误

不要把可提前过滤的条件放到 `HAVING`，这会扩大聚合输入。

## 调优提示

先 `WHERE` 后 `GROUP BY` 再 `HAVING`，尽量减少进入聚合阶段的数据量。

## 相关主题

- [GROUP BY 分组](GROUP-BY-分组.md)
- [GROUP BY 调优](../08-Performance-Diagnostics/GROUP-BY-调优.md)


