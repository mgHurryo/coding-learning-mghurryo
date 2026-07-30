---
title: TRUNCATE 与 DELETE 区别
description: 比较清空表数据和按条件删除记录的差异。
tags:
  - MySQL
  - DML
category: MySQL
---

# TRUNCATE 与 DELETE 区别

## 方法定位

比较清空表数据和按条件删除记录的差异。

## 基本语法

```sql
TRUNCATE TABLE temp_import; -- 清空整表
DELETE FROM temp_import WHERE batch_id = 1001; -- 条件删除
```

## 示例场景

临时导入表可用 `TRUNCATE` 重建；业务表通常使用带条件的 `DELETE` 或逻辑删除。

## 使用边界

`TRUNCATE` 适合清空整张临时表；`DELETE` 适合按条件删除。

## 常见错误

不要用 `TRUNCATE` 清理业务表局部数据；不要忽略自增值重置和隐式提交影响。

## 调优提示

大表清理要考虑分区、归档和分批删除，而不是简单一条语句解决。

## 相关主题

- [DELETE 条件删除](DELETE-条件删除.md)
- [DROP TABLE 删除表](DROP-TABLE-删除表.md)


