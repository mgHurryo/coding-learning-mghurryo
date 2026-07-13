---
title: JOIN 调优
description: 说明 MySQL JOIN 调优中驱动表、连接字段索引、返回行数和改写策略。
tags:
  - MySQL
  - Performance
  - JOIN
category: MySQL
---

# JOIN 调优

## 速览

JOIN 调优的核心是控制参与连接的行数，并让连接条件能使用索引。小结果集驱动大表通常更稳，但最终以优化器计划和真实数据为准。

## 核心机制

MySQL 常用 nested loop 思路执行连接：从驱动表取一批行，再到被驱动表按连接条件查找。被驱动表连接字段若没有索引，可能对每行驱动记录都扫描大量数据。WHERE 过滤应尽早缩小驱动表。

## SQL/配置示例

```sql
EXPLAIN
SELECT a.id, a.title, u.username
FROM article a
JOIN user u ON a.user_id = u.id
WHERE a.status = 1
ORDER BY a.create_time DESC
LIMIT 20;
```

## 项目落地

列表接口 JOIN 前先确认是否真的需要实时连表。高频读可以通过冗余展示字段、缓存、异步宽表降低 JOIN 压力，但要管理一致性。

## 常见错误

不要忘记 ON 条件；不要让大表和大表在无索引字段上连接；不要 SELECT * 导致大量回表和网络传输。

## 面试追问

- JOIN 为什么需要连接字段索引？
- 驱动表如何选择？
- 大表 JOIN 慢有哪些改法？

## 排障/边界

用 EXPLAIN 看表访问顺序、type、key、rows 和 Extra。若连接前过滤不足，先优化 WHERE 和索引，再考虑业务拆分。

## 相关主题

- [INNER JOIN](../04-Query-Methods/INNER-JOIN.md)
- [Composite Index 联合索引](../05-Indexing/Composite-Index-联合索引.md)
- [EXPLAIN 使用方法](EXPLAIN-使用方法.md)
