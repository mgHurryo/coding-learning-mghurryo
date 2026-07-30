---
title: INSERT IGNORE
description: 用于插入时忽略部分错误，常见于唯一键冲突时跳过重复数据。
tags:
  - MySQL
  - DML
category: MySQL
---

# INSERT IGNORE

## 方法定位

用于插入时忽略部分错误，常见于唯一键冲突时跳过重复数据。

## 基本语法

```sql
INSERT IGNORE INTO user (username) VALUES ("tom");
```

## 示例场景

导入外部用户名列表时，已有用户名可跳过，避免整个批次失败。

## 使用边界

适合允许跳过冲突的导入场景；不适合必须知道失败原因的关键写入。

## 常见错误

不要用它掩盖数据质量问题；不要忽略实际受影响行数。

## 调优提示

配合唯一索引做去重时，先确认冲突比例，冲突过高也会消耗写入成本。

## 相关主题

- [UNIQUE 唯一约束](UNIQUE-唯一约束.md)
- [INSERT ON DUPLICATE KEY UPDATE](INSERT-ON-DUPLICATE-KEY-UPDATE.md)


