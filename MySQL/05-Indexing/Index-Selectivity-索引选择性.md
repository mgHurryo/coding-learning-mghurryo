---
title: Index Selectivity 索引选择性
description: 说明索引选择性、基数和为什么低区分度字段不一定适合单独建索引。
tags:
  - MySQL
  - Index
category: MySQL
---

# Index Selectivity 索引选择性

## 速览

索引选择性表示字段能把数据区分开的能力。选择性越高，索引越容易把扫描范围缩小；选择性很低的字段单独建索引，常常收益有限。

## 核心机制

可以粗略理解为 `distinct_count / row_count`。手机号、订单号、用户 id 通常选择性高；性别、状态位、是否删除选择性低。低选择性字段不是不能进索引，而是更适合作为联合索引的一部分，配合高选择性字段和排序字段使用。

## SQL/配置示例

```sql
SHOW INDEX FROM article;
SELECT COUNT(DISTINCT status) / COUNT(*) AS selectivity FROM article;
```

## 项目落地

设计索引时先看业务查询是否稳定，再看字段选择性。比如 `(user_id, status, create_time)` 常比单独给 `status` 建索引更有效，因为 `user_id` 先缩小范围，`status` 再过滤，`create_time` 支持排序。

## 常见错误

不要只因为字段出现在 WHERE 就建索引；不要把 `deleted`、`status` 这类低基数字段单独建成大量索引；不要忽略数据分布倾斜。

## 面试追问

- 什么是索引选择性？
- 低选择性字段一定不能建索引吗？
- 为什么联合索引字段顺序要看等值、范围、排序和选择性？

## 排障/边界

`SHOW INDEX` 的 Cardinality 是估算值，可能受统计信息影响。判断索引价值要结合 `EXPLAIN`、真实数据分布和慢查询样本。

## 相关主题

- [[MySQL/99-Common-Concepts/选择性与基数|选择性与基数]]
- [[MySQL/05-Indexing/Composite-Index-联合索引|Composite Index 联合索引]]
- [[MySQL/08-Performance-Diagnostics/EXPLAIN-rows-字段|EXPLAIN rows 字段]]
