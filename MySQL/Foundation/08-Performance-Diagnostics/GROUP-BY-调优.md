---
title: GROUP BY 调优
description: 优化分组聚合输入行数、分组字段索引和临时表使用。
tags:
  - MySQL
  - Performance
category: MySQL
---

# GROUP BY 调优

## 方法定位

优化分组聚合输入行数、分组字段索引和临时表使用。

## 基本语法

```sql
SELECT category_id, COUNT(*) FROM article WHERE status = 1 GROUP BY category_id;
```

## 示例场景

统计已发布文章各分类数量时，先过滤 `status` 再分组。

## 使用边界

适合实时轻量汇总；高频大表统计应考虑汇总表或缓存。

## 常见错误

不要让接口每次都全表分组统计；不要把可提前过滤的条件放到 HAVING。

## 调优提示

使用 `(status, category_id)` 之类索引减少扫描和分组成本。

## 相关主题

- [GROUP BY 分组](GROUP-BY-分组.md)
- [HAVING 分组后过滤](HAVING-分组后过滤.md)


