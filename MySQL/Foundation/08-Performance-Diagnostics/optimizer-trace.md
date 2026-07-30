---
title: optimizer trace
description: 说明 optimizer trace 如何查看优化器选择访问路径、索引和 join 顺序的原因。
tags:
  - MySQL
  - Performance
  - Optimizer
category: MySQL
---

# optimizer trace

## 速览

optimizer trace 用来观察优化器决策过程。它比 `EXPLAIN` 更接近“为什么选这个计划”，适合分析优化器为什么没有选择预期索引或 join 顺序。

## 核心机制

开启 trace 后，MySQL 会把优化器在解析、估算、比较访问路径和选择计划时的过程写入 `information_schema.optimizer_trace`。它包含候选索引、成本估算、条件处理和最终选择原因。

## SQL/配置示例

```sql
SET optimizer_trace='enabled=on';
SELECT * FROM article WHERE user_id = 1 ORDER BY create_time DESC LIMIT 20;
SELECT trace FROM information_schema.optimizer_trace\G
SET optimizer_trace='enabled=off';
```

## 项目落地

当 `EXPLAIN` 只能告诉你“用了哪个索引”，却不能解释“为什么不用另一个索引”时，用 optimizer trace 辅助判断统计信息、成本模型或 SQL 写法问题。

## 常见错误

不要长期在生产全局开启；trace 信息较大，应该按会话、按问题 SQL 使用。

## 面试追问

- optimizer trace 解决什么问题？
- 优化器为什么可能不选你认为最好的索引？
- 成本估算受哪些信息影响？

## 相关主题

- [EXPLAIN 使用方法](EXPLAIN-使用方法.md)
- [Index Selectivity 索引选择性](Index-Selectivity-索引选择性.md)

## 参考资料

- [MySQL 8.4 Reference Manual - MySQL Internals: Tracing the Optimizer](https://dev.mysql.com/doc/refman/8.4/en/optimizer-tracing.html)
