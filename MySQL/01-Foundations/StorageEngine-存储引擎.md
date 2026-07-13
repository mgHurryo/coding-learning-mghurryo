---
title: StorageEngine 存储引擎
description: 说明存储引擎决定表的事务、锁、索引和持久化能力，业务表通常使用 InnoDB。
tags:
  - MySQL
  - Basics
category: MySQL
---

# StorageEngine 存储引擎

## 方法定位

说明存储引擎决定表的事务、锁、索引和持久化能力，业务表通常使用 InnoDB。

## 基本语法

```sql
CREATE TABLE user (id BIGINT PRIMARY KEY) ENGINE=InnoDB;
```

## 示例场景

Spring Boot 后端项目中的核心业务表一般使用 InnoDB，以获得事务、行级锁和崩溃恢复能力。

## 使用边界

InnoDB 适合大多数 OLTP 场景；特殊临时表或全文检索需求再评估其他方案。

## 常见错误

不要在需要事务的业务表上使用不支持事务的引擎。

## 调优提示

InnoDB 的主键设计会影响聚簇索引结构，主键应短、稳定、递增或近似递增。

## 相关主题

- [Primary Index 主键索引](../05-Indexing/Primary-Index-主键索引.md)
- [Transaction 事务](../06-Transaction-Lock/Transaction-事务.md)


