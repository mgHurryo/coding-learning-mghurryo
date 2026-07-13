---
title: GROUP BY 分组
description: 用于按一个或多个字段分组并计算聚合结果。
tags:
  - MySQL
  - Query
category: MySQL
---

# GROUP BY 分组

## 方法定位

用于按一个或多个字段分组并计算聚合结果。

## 基本语法

```sql
SELECT status, COUNT(*) FROM article GROUP BY status;
```

## 示例场景

统计不同文章状态下的数量，如草稿、已发布、已删除。

## 使用边界

适合维度数量有限的汇总；高基数字段分组成本较高。

## 常见错误

不要把 `WHERE` 和 `HAVING` 混用；不要选择非分组字段造成语义混乱。

## 调优提示

分组字段与过滤字段可组合索引，减少临时表和排序成本。

## 相关主题

- [HAVING 分组后过滤](HAVING-分组后过滤.md)
- [GROUP BY 调优](../08-Performance-Diagnostics/GROUP-BY-调优.md)


