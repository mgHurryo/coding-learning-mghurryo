---
title: MySQL 函数百科
description: 面向初学者的 MySQL 8.4 内置函数索引，按用途查找字符串、数值、日期时间、NULL、类型转换、JSON、聚合和窗口函数。
tags:
  - MySQL
  - Function
  - MOC
category: MySQL
---

# MySQL 函数百科

> 函数接收一个或多个值，经过计算后返回一个值。它可以出现在 `SELECT`、`WHERE`、`ORDER BY`、`GROUP BY`、`HAVING`、`ON` 和 `UPDATE` 表达式中。本文以 MySQL 8.4 LTS 为主线，示例默认使用 `article` 表。

## 先理解三个概念

- **标量函数**：一行输入产生一个结果，例如 `UPPER(name)`。
- **聚合函数**：多行输入汇总成一个结果，例如 `COUNT(*)`；通常和 `GROUP BY` 一起使用。
- **窗口函数**：计算相关行但保留每一行，例如 `ROW_NUMBER() OVER (...)`。

函数不会自动修改原始数据；只有把它放进 `UPDATE`、`INSERT` 等写操作，结果才会写回表中。

## 学习地图

| 分类 | 代表函数 | 适合解决的问题 |
| :--- | :--- | :--- |
| [字符串函数](字符串函数.md) | `CONCAT`、`SUBSTRING`、`REPLACE`、`TRIM` | 拼接、截取、清洗和查找文本 |
| [数值函数](数值函数.md) | `ROUND`、`FLOOR`、`CEIL`、`ABS` | 四舍五入、取整、绝对值和余数 |
| [日期时间函数](日期时间函数.md) | `NOW`、`DATE_ADD`、`DATEDIFF`、`DATE_FORMAT` | 当前时间、日期运算、格式化 |
| [NULL 与流程控制函数](NULL-与-流程控制函数.md) | `COALESCE`、`IFNULL`、`NULLIF`、`IF` | 缺失值兜底和条件分支 |
| [类型转换与系统信息函数](类型转换与系统信息函数.md) | `CAST`、`CONVERT`、`DATABASE`、`VERSION` | 类型转换、环境信息和调试 |
| [JSON 函数](JSON函数.md) | `JSON_EXTRACT`、`JSON_SET`、`JSON_CONTAINS` | 读取、修改和检索 JSON 文档 |
| [聚合函数](Aggregate-聚合函数.md) | `COUNT`、`SUM`、`AVG`、`MAX`、`MIN` | 分组统计和报表汇总 |
| [窗口函数](Window-Functions-窗口函数.md) | `ROW_NUMBER`、`RANK`、`LAG` | 排名、累计和组内分析 |

## 写函数时的通用检查

1. 先确认输入类型；字符串、数字、日期和 JSON 不要混用。
2. 预先考虑 `NULL`：很多函数遇到 `NULL` 会返回 `NULL`。
3. 结果是否需要别名？报表和应用读取时应使用清晰的 `AS` 别名。
4. 是否对索引列套了函数？例如 `WHERE DATE(create_time) = ...` 可能无法直接使用普通索引。
5. 是否有版本要求？JSON、窗口函数和部分正则函数依赖 MySQL 8.0+。
6. 展示层能完成的格式化，不要全部挪到数据库中；数据库优先保证筛选和计算正确。

## 快速示例

```sql
SELECT
    UPPER(username) AS display_name,
    ROUND(view_count / 1000, 1) AS views_in_k,
    DATE_FORMAT(create_time, '%Y-%m-%d') AS created_date,
    COALESCE(title, '(untitled)') AS safe_title
FROM article
WHERE create_time >= '2026-01-01';
```

## 参考

- [MySQL 8.4 Functions and Operators](https://dev.mysql.com/doc/refman/8.4/en/functions.html)
- [DQL MOC](DQL-MOC.md)
