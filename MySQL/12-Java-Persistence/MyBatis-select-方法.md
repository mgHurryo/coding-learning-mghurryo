---
title: MyBatis select 方法
description: 在 Mapper 中把 Java 查询方法映射为 SQL SELECT。
tags:
  - MySQL
  - Java-Persistence
category: MySQL
---

# MyBatis select 方法

## 方法定位

在 Mapper 中把 Java 查询方法映射为 SQL SELECT。

## 基本语法

```java
@Select("SELECT id, username FROM user WHERE id = #{id}")
User findById(Long id);
```

## 示例场景

登录成功后根据用户 ID 查询用户基础信息，返回 `User` 实体或专用查询对象。

## 使用边界

适合简单查询；复杂动态条件建议使用 XML Mapper 或动态 SQL。

## 常见错误

不要使用字符串拼接用户输入；不要直接返回包含敏感字段的实体给接口层。

## 调优提示

查询字段应和返回对象匹配，避免 SELECT *；慢查询仍要回到 SQL 和索引层分析。

## 相关主题

- [[MySQL/03-SQL-DML/SELECT-基础查询|SELECT 基础查询]]
- [[MySQL/12-Java-Persistence/MyBatis-参数绑定|MyBatis 参数绑定]]


