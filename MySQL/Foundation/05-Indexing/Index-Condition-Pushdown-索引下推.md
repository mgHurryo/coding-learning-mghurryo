---
title: Index Condition Pushdown 索引下推
description: 索引下推让存储引擎在遍历索引时先判断部分条件，减少回表次数。
tags:
  - MySQL
  - Index
category: MySQL
---

# Index Condition Pushdown 索引下推

## 方法定位

索引下推让存储引擎在遍历索引时先判断部分条件，减少回表次数。

## 基本语法

```sql
SELECT * FROM user WHERE username LIKE "tom%" AND age = 18;
```

## 示例场景

联合索引上前缀匹配后，后续条件可能在引擎层进一步过滤。

## 使用边界

适合理解 `EXPLAIN Extra` 中 `Using index condition`。

## 常见错误

不要把索引下推当作替代合理索引设计的万能优化。

## 调优提示

结合联合索引和可下推条件，减少回表行数。

## 相关主题

- [EXPLAIN Extra 字段](EXPLAIN-Extra-字段.md)
- [Composite Index 联合索引](Composite-Index-联合索引.md)


