---
title: DROP TABLE 删除表
description: 用于删除指定表结构和表内所有数据，是不可逆风险较高的操作。
tags:
  - MySQL
  - DDL
category: MySQL
---

# DROP TABLE 删除表

## 方法定位

用于删除指定表结构和表内所有数据，是不可逆风险较高的操作。

## 基本语法

```sql
DROP TABLE article;
```

## 示例场景

删除练习环境中的临时表；生产环境通常先下线引用，再备份，再删除。

## 使用边界

适合确认表已废弃的场景；不适合只想清空表数据的场景。

## 常见错误

不要用 `DROP TABLE` 处理普通业务删除；不要忽略外键或应用依赖。

## 调优提示

删除表本身不是查询调优手段，应作为生命周期治理动作。

## 相关主题

- [TRUNCATE 与 DELETE 区别](../03-SQL-DML/TRUNCATE-与-DELETE-区别.md)
- [ALTER TABLE 修改表](ALTER-TABLE-修改表.md)


