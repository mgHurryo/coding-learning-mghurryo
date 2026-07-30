---
title: MyBatis insert 方法
description: 在 Mapper 中把 Java 新增方法映射为 SQL INSERT。
tags:
  - MySQL
  - Java-Persistence
category: MySQL
---

# MyBatis insert 方法

## 方法定位

在 Mapper 中把 Java 新增方法映射为 SQL INSERT。

## 基本语法

```java
@Insert("INSERT INTO user(username, password, create_time) VALUES(#{username}, #{password}, NOW())")
int insert(User user);
```

## 示例场景

用户注册时插入用户名、密码摘要和创建时间。

## 使用边界

适合单条新增；批量导入应使用批量操作或 XML。

## 常见错误

不要保存明文密码；不要用 `${}` 拼接字段值。

## 调优提示

高频写入关注唯一索引冲突、事务边界和批量提交。

## 相关主题

- [INSERT 单行插入](INSERT-单行插入.md)
- [MyBatis 批量操作](MyBatis-批量操作.md)


