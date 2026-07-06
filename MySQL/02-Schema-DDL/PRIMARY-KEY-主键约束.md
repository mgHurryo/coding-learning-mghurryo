---
title: PRIMARY KEY 主键约束
description: 用于唯一标识表中的每一行，并在 InnoDB 中决定聚簇索引组织方式。
tags:
  - MySQL
  - DDL
category: MySQL
---

# PRIMARY KEY 主键约束

## 方法定位

用于唯一标识表中的每一行，并在 InnoDB 中决定聚簇索引组织方式。

## 基本语法

```sql
id BIGINT PRIMARY KEY AUTO_INCREMENT
```

## 示例场景

`user.id` 常作为主键，业务查询再通过 `username` 等字段建立唯一索引。

## 使用边界

每张业务表通常应有主键；联合主键适合关系表，但要评估索引宽度。

## 常见错误

不要频繁更新主键；不要选择过长字符串作为大表主键。

## 调优提示

InnoDB 主键越短越稳定，二级索引叶子节点携带主键的成本越低。

## 相关主题

- [[MySQL/05-Indexing/Primary-Index-主键索引|Primary Index 主键索引]]
- [[MySQL/01-Foundations/StorageEngine-存储引擎|StorageEngine 存储引擎]]


