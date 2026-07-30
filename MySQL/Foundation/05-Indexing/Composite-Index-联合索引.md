---
title: Composite Index 联合索引
description: 说明联合索引字段顺序、最左前缀、范围条件、排序和覆盖索引的综合设计。
tags:
  - MySQL
  - Index
category: MySQL
---

# Composite Index 联合索引

## 速览

联合索引由多个字段组成，适合稳定的多条件过滤、排序和覆盖查询。字段顺序决定它能支持哪些查询模式。

## 核心机制

联合索引按字段顺序构建 B+Tree，例如 `(user_id, status, create_time)` 先按 `user_id` 排，再按 `status`，最后按 `create_time`。查询需要从最左字段开始连续匹配；范围条件之后的字段通常难以继续用于精确定位，但仍可能用于覆盖或部分排序。

## SQL/配置示例

```sql
CREATE INDEX idx_article_user_status_time
ON article(user_id, status, create_time);

SELECT id, title
FROM article
WHERE user_id = 1 AND status = 1
ORDER BY create_time DESC
LIMIT 20;
```

## 项目落地

设计联合索引时先收集高频 SQL：等值过滤字段靠前，范围和排序字段靠后，查询返回字段少时考虑覆盖索引。不要为每个条件组合都建一条索引。

## 常见错误

不要机械套“选择性最高放最前”而忽略排序和查询形态；不要把低频 SQL 的字段塞进核心索引；不要创建大量前缀重复的索引。

## 面试追问

- 最左前缀原则是什么？
- 范围条件为什么会影响后续字段使用？
- `(a,b,c)` 能否支持 `WHERE b=?`？

## 排障/边界

用 `EXPLAIN` 看 `key`、`key_len`、`rows` 和 `Extra`，确认实际用到了哪些字段。再用真实参数验证慢查询是否改善。

## 相关主题

- [Leftmost Prefix 最左前缀原则](Leftmost-Prefix-最左前缀原则.md)
- [Covering Index 覆盖索引](Covering-Index-覆盖索引.md)
- [Index Selectivity 索引选择性](Index-Selectivity-索引选择性.md)
