---
title: MyBatis 参数绑定
description: 说明 `#{}` 与 `${}` 的差异，以及 Mapper 方法参数如何绑定到 SQL。
tags:
  - MySQL
  - Java-Persistence
category: MySQL
---

# MyBatis 参数绑定

## 方法定位

说明 `#{}` 与 `${}` 的差异，以及 Mapper 方法参数如何绑定到 SQL。

## 基本语法

```java
@Select("SELECT * FROM user WHERE username = #{username}")
User findByUsername(String username);
```

## 示例场景

登录查询中用户名来自用户输入，必须使用 `#{}` 预编译绑定。

## 使用边界

`#{}` 适合值绑定；`${}` 只适合白名单控制后的表名、列名等 SQL 片段。

## 常见错误

不要把用户输入放进 `${}`；不要在多参数方法里省略清晰的 `@Param`。

## 调优提示

预编译绑定既提升安全性，也有利于数据库复用执行计划。

## 相关主题

- [MyBatis 数据访问](MyBatis-数据访问.md)
- [WHERE 调优](WHERE-调优.md)


