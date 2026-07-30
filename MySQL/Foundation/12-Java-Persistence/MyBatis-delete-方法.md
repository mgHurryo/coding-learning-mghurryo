---
title: MyBatis delete 方法
description: 在 Mapper 中把 Java 删除方法映射为 SQL DELETE 或逻辑删除 UPDATE。
tags:
  - MySQL
  - Java-Persistence
category: MySQL
---

# MyBatis delete 方法

## 方法定位

在 Mapper 中把 Java 删除方法映射为 SQL DELETE 或逻辑删除 UPDATE。

## 基本语法

```java
@Delete("DELETE FROM article WHERE id = #{id} AND user_id = #{userId}")
int deleteById(Long id, Long userId);
```

## 示例场景

用户删除文章时，必须同时校验文章 ID 和用户 ID，避免删除他人数据。

## 使用边界

物理删除适合临时数据；业务数据通常优先逻辑删除。

## 常见错误

不要只按 ID 删除而忽略权限边界；不要无条件删除。

## 调优提示

删除条件命中索引，大批量删除要分批或转为归档清理任务。

## 相关主题

- [DELETE 条件删除](DELETE-条件删除.md)
- [Soft Delete 逻辑删除](Soft-Delete-逻辑删除.md)


