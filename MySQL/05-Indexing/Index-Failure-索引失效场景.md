---
title: Index Failure 索引失效场景
description: 总结 MySQL 索引无法有效使用的常见原因、识别方式和改写方向。
tags:
  - MySQL
  - Index
  - Performance
category: MySQL
---

# Index Failure 索引失效场景

## 速览

索引失效不是索引“坏了”，而是 SQL 写法、数据分布或优化器成本判断导致索引不能有效缩小扫描范围。

## 核心机制

常见场景包括：对索引列使用函数或表达式、隐式类型转换、左模糊 LIKE、联合索引不满足最左前缀、OR 条件无法合并、范围条件后续字段无法继续定位、低选择性导致优化器选择全表扫描、统计信息过旧。

## SQL/配置示例

```sql
-- 不推荐
SELECT * FROM user WHERE DATE(create_time) = '2026-07-04';

-- 推荐
SELECT * FROM user
WHERE create_time >= '2026-07-04'
  AND create_time <  '2026-07-05';
```

## 项目落地

接口参数类型要和字段类型一致，避免字符串查数字或数字查字符串。模糊搜索不要指望 `%keyword%` 普通 B+Tree 索引高效，必要时评估全文索引或搜索系统。

## 常见错误

不要只看 `key` 是否为空；即使用了索引，也可能扫描大量行。不要在高频接口中把字段包函数后再过滤。

## 面试追问

- 哪些 SQL 写法容易导致索引失效？
- 隐式类型转换为什么危险？
- LIKE 哪些写法能用上索引？

## 排障/边界

先用 `EXPLAIN` 看访问方式和 rows，再用真实参数判断过滤比例。必要时更新统计信息或重写 SQL。

## 相关主题

- [Leftmost Prefix 最左前缀原则](Leftmost-Prefix-最左前缀原则.md)
- [LIKE 模糊查询](../04-Query-Methods/LIKE-模糊查询.md)
- [EXPLAIN 使用方法](../08-Performance-Diagnostics/EXPLAIN-使用方法.md)
