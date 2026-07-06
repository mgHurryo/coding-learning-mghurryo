---
title: EXPLAIN ANALYZE
description: 说明 EXPLAIN ANALYZE 如何用真实执行统计验证优化器估算和 SQL 调优效果。
tags:
  - MySQL
  - Performance
  - EXPLAIN
category: MySQL
---

# EXPLAIN ANALYZE

## 速览

`EXPLAIN ANALYZE` 会实际执行查询，并返回执行计划中各节点的真实耗时和行数。它适合验证优化器估算是否准确，也适合对比调优前后的效果。

## 核心机制

普通 `EXPLAIN` 主要展示优化器估算；`EXPLAIN ANALYZE` 会执行语句并收集运行时信息。它能暴露“估算 rows 很小但实际很多”“某个 nested loop 节点耗时异常”“过滤比例和预期不一致”等问题。

## SQL/配置示例

```sql
EXPLAIN ANALYZE
SELECT a.id, a.title
FROM article a
WHERE a.user_id = 1
ORDER BY a.create_time DESC
LIMIT 20;
```

## 项目落地

在测试环境或可控窗口使用，尤其适合慢 SQL 重构、索引调整后验证真实收益。生产上要谨慎，因为语句会真的执行。

## 常见错误

不要对写语句或高成本查询随意执行；不要只看总耗时，还要看每个节点的 actual rows 和 loops。

## 面试追问

- `EXPLAIN` 和 `EXPLAIN ANALYZE` 有什么区别？
- 为什么估算行数和实际行数会差很多？
- 如何用它验证索引优化是否有效？

## 相关主题

- [[MySQL/08-Performance-Diagnostics/EXPLAIN-使用方法|EXPLAIN 使用方法]]
- [[MySQL/08-Performance-Diagnostics/optimizer-trace|optimizer trace]]
- [[MySQL/08-Performance-Diagnostics/慢-SQL-排查流程|慢 SQL 排查流程]]

## 参考资料

- [MySQL 8.4 Reference Manual - Obtaining Execution Plan Information](https://dev.mysql.com/doc/refman/8.4/en/execution-plan-information.html)
