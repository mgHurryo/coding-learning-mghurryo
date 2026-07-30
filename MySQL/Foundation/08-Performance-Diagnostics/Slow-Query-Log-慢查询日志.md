---
title: Slow Query Log 慢查询日志
description: 慢查询日志用于记录超过阈值的 SQL，是定位线上慢 SQL 的入口。
tags:
  - MySQL
  - Performance
category: MySQL
---

# Slow Query Log 慢查询日志

## 方法定位

慢查询日志用于记录超过阈值的 SQL，是定位线上慢 SQL 的入口。

## 基本语法

```sql
SET GLOBAL slow_query_log = ON;
SET GLOBAL long_query_time = 1;
```

## 示例场景

接口响应变慢时，结合慢查询日志找到具体 SQL、执行时间和扫描量。

## 使用边界

适合诊断真实运行问题；开启和阈值应按环境策略配置。

## 常见错误

不要只靠本地感觉判断慢 SQL；不要在生产随意长期开启过低阈值。

## 调优提示

慢日志发现问题后，用 `EXPLAIN`、索引、SQL 改写和业务缓存逐步处理。

## 相关主题

- [EXPLAIN 使用方法](EXPLAIN-使用方法.md)
- [SELECT 调优](SELECT-调优.md)


