---
title: Primary Index 主键索引
description: 主键索引唯一标识记录，在 InnoDB 中也是聚簇索引。
tags:
  - MySQL
  - Index
category: MySQL
---

# Primary Index 主键索引

## 方法定位

主键索引唯一标识记录，在 InnoDB 中也是聚簇索引。

## 基本语法

```sql
PRIMARY KEY (id)
```

## 示例场景

`user.id` 作为主键，二级索引最终会指向主键值。

## 使用边界

适合稳定唯一标识；业务自然键变化频繁时不适合作为主键。

## 常见错误

不要使用过长主键拖累所有二级索引；不要频繁变更主键。

## 调优提示

自增或近似递增主键更利于页写入局部性，但分布式系统需平衡热点和生成策略。

## 相关主题

- [PRIMARY KEY 主键约束](PRIMARY-KEY-主键约束.md)
- [StorageEngine 存储引擎](StorageEngine-存储引擎.md)


