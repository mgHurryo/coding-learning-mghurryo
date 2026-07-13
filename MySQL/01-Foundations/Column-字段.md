---
title: Column 字段
description: 说明字段用于描述表中每一列的数据含义、类型、是否允许为空和默认值。
tags:
  - MySQL
  - Basics
category: MySQL
---

# Column 字段

## 方法定位

说明字段用于描述表中每一列的数据含义、类型、是否允许为空和默认值。

## 基本语法

```sql
username VARCHAR(50) NOT NULL DEFAULT ""
```

## 示例场景

`user.username` 表示用户名，`article.create_time` 表示文章创建时间，字段名应稳定且语义清楚。

## 使用边界

适合表达原子数据；不适合在一个字段中混合保存多个可查询属性。

## 常见错误

不要滥用 `TEXT` 替代明确长度的 `VARCHAR`；不要让重要状态字段没有默认值。

## 调优提示

字段类型越贴合实际范围，索引体积和 I/O 成本越低。

## 相关主题

- [DataType 数据类型](DataType-数据类型.md)
- [ALTER TABLE 修改表](../02-Schema-DDL/ALTER-TABLE-修改表.md)


