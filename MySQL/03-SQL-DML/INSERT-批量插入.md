---
title: INSERT 批量插入
description: 用于一次 SQL 插入多行记录，减少网络往返和语句解析成本。
tags:
  - MySQL
  - DML
category: MySQL
---

# INSERT 批量插入

## 方法定位

用于一次 SQL 插入多行记录，减少网络往返和语句解析成本。

## 基本语法

```sql
INSERT INTO category (category_name, create_time) VALUES ("Java", NOW()), ("MySQL", NOW());
```

## 示例场景

初始化文章分类或批量导入用户标签时，可以一次插入多行。

## 使用边界

适合中小批量写入；超大批量应分批并控制事务大小。

## 常见错误

不要一次提交过大的 values 列表；不要忽略唯一键冲突。

## 调优提示

批量大小应结合 `max_allowed_packet`、redo 日志压力和连接池占用测试。

## 相关主题

- [[MySQL/08-Performance-Diagnostics/Batch-Insert-批量写入调优|Batch Insert 批量写入调优]]
- [[MySQL/06-Transaction-Lock/Transaction-事务|Transaction 事务]]


