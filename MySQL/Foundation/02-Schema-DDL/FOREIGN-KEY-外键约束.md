---
title: FOREIGN KEY 外键约束
description: 用于在数据库层保证子表字段引用父表主键或唯一键的完整性。
tags:
  - MySQL
  - DDL
category: MySQL
---

# FOREIGN KEY 外键约束

## 方法定位

用于在数据库层保证子表字段引用父表主键或唯一键的完整性。

## 基本语法

```sql
FOREIGN KEY (user_id) REFERENCES user(id)
```

## 示例场景

`article.user_id` 可以引用 `user.id`，防止文章指向不存在的用户。

## 使用边界

适合强一致关系；在高并发业务中也可用应用层约束替代，但要承担一致性责任。

## 常见错误

不要只建外键而忽略对应索引；不要让级联删除误删大量业务数据。

## 调优提示

外键会增加写入校验成本，设计前应评估写入频率和数据治理方式。

## 相关主题

- [PRIMARY KEY 主键约束](PRIMARY-KEY-主键约束.md)
- [Row Lock 行锁](Row-Lock-行锁.md)


