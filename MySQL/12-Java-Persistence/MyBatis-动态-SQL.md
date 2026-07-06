---
title: MyBatis 动态 SQL
description: 用于根据参数动态拼装 WHERE、SET、IN 等 SQL 片段。
tags:
  - MySQL
  - Java-Persistence
category: MySQL
---

# MyBatis 动态 SQL

## 方法定位

用于根据参数动态拼装 WHERE、SET、IN 等 SQL 片段。

## 基本语法

```xml
<select id="list" resultType="Article">
  SELECT id, title FROM article
  <where>
    <if test="status != null">AND status = #{status}</if>
  </where>
</select>
```

## 示例场景

文章列表可以按状态、分类、关键词等可选条件动态查询。

## 使用边界

适合条件较多的查询和更新；简单固定 SQL 不必过度使用动态 SQL。

## 常见错误

不要让动态 SQL 生成语法错误；不要把排序字段直接透传拼接。

## 调优提示

动态条件变化会影响索引选择，应为高频条件组合设计索引。

## 相关主题

- [[MySQL/04-Query-Methods/WHERE-条件过滤|WHERE 条件过滤]]
- [[MySQL/05-Indexing/Composite-Index-联合索引|Composite Index 联合索引]]


