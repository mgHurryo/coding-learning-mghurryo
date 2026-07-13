---
title: Connection Pool 连接池调优
description: 说明应用连接池大小、等待时间和数据库连接数之间的平衡。
tags:
  - MySQL
  - Performance
category: MySQL
---

# Connection Pool 连接池调优

## 方法定位

说明应用连接池大小、等待时间和数据库连接数之间的平衡。

## 基本语法

```sql
spring.datasource.hikari.maximum-pool-size=20
```

## 示例场景

Spring Boot 接口并发上升时，连接池过小会等待，过大可能压垮数据库。

## 使用边界

适合工程侧吞吐优化；不能替代慢 SQL 和索引优化。

## 常见错误

不要盲目把连接池调很大；不要让线程池远大于数据库承受能力。

## 调优提示

结合接口耗时、数据库 CPU、活跃连接、等待队列和慢 SQL 一起调。

## 相关主题

- [DataSource 数据源](../12-Java-Persistence/DataSource-数据源.md)
- [多线程](../../Java/Advanced/多线程.md)


