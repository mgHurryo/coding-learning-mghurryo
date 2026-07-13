---
title: Secondary Index 二级索引
description: 说明 InnoDB 二级索引叶子节点保存主键值、回表成本和覆盖索引优化方向。
tags:
  - MySQL
  - Index
category: MySQL
---

# Secondary Index 二级索引

## 速览

二级索引是非主键索引。InnoDB 二级索引叶子节点保存索引列和对应主键值，查到主键后如果还需要其它列，就要回到聚簇索引读取整行，这个过程叫回表。

## 核心机制

例如 `idx_user_status(user_id, status)` 命中后，叶子节点可以拿到 `user_id`、`status` 和主键 `id`。如果查询只要这些字段和 `id`，可以覆盖索引；如果还要 `content`，就要按 `id` 回聚簇索引找完整行。回表次数越多，随机 I/O 和 CPU 成本越高。

## SQL/配置示例

```sql
CREATE INDEX idx_article_user_status ON article(user_id, status);
EXPLAIN SELECT id, title FROM article WHERE user_id = 1 AND status = 1;
```

## 项目落地

列表页常用二级索引定位候选记录，再结合覆盖索引减少回表。不要为了覆盖所有查询把大字段塞进索引，索引越宽，写入和缓存成本越高。

## 常见错误

不要以为 `key` 有值就一定快；二级索引命中但回表百万次仍然会慢。不要在主键过长时随意创建大量二级索引，因为每个二级索引都要携带主键值。

## 面试追问

- 二级索引叶子节点为什么保存主键？
- 什么是回表？如何减少回表？
- 为什么主键设计会影响所有二级索引？

## 排障/边界

看到 `Using index` 通常代表覆盖索引；看到扫描行数很大且查询列很多，要检查是否大量回表。

## 相关主题

- [回表](../99-Common-Concepts/回表.md)
- [Covering Index 覆盖索引](Covering-Index-覆盖索引.md)
- [Primary Index 主键索引](Primary-Index-主键索引.md)
