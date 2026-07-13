---
title: MyBatis update 方法
description: 在 Mapper 中把 Java 更新方法映射为 SQL UPDATE。
tags:
  - MySQL
  - Java-Persistence
category: MySQL
---

# MyBatis update 方法

## 方法定位

在 Mapper 中把 Java 更新方法映射为 SQL UPDATE。

## 基本语法

```java
@Update("UPDATE article SET title = #{title}, update_time = NOW() WHERE id = #{id} AND user_id = #{userId}")
int updateTitle(Article article);
```

## 示例场景

用户编辑自己的文章标题时，同时使用文章 ID 和用户 ID 限定更新范围。

## 使用边界

适合明确条件更新；动态更新多字段时 XML 更易维护。

## 常见错误

不要漏写业务归属条件；不要把空字段无意覆盖进数据库。

## 调优提示

更新条件应命中索引，返回影响行数用于判断是否更新成功。

## 相关主题

- [UPDATE 条件更新](../03-SQL-DML/UPDATE-条件更新.md)
- [Row Lock 行锁](../06-Transaction-Lock/Row-Lock-行锁.md)


