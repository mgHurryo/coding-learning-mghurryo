---
title: INSERT 单行插入
description: 用于向表中插入一条新记录，是新增业务数据的基础写法。
tags:
  - MySQL
  - DML
category: MySQL
---

# INSERT 单行插入

## 方法定位

用于向表中插入一条新记录，是新增业务数据的基础写法。

## 基本语法

```sql

// 指定数据插入
INSERT INTO user (username, password, create_time) VALUES ("tom", "hash", NOW());

// 全行插入
INSERT INTO user VALUES ("CCC", "hash", NOW());
```

## 示例场景

用户注册时向 `user` 表插入用户名、密码摘要和创建时间。

## 使用边界

适合单条业务写入；高吞吐导入应考虑批量插入。

## 常见错误

不要省略字段列表；不要把明文密码写入数据库；不要拼接用户输入。

## 调优提示

插入高频表时关注唯一索引数量、事务大小和批量提交策略。

## 相关主题

- [INSERT 批量插入](INSERT-批量插入.md)
- [DEFAULT 默认值](../02-Schema-DDL/DEFAULT-默认值.md)


