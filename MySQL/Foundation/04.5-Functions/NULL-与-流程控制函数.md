---
title: MySQL NULL 与流程控制函数
description: 面向初学者讲解 NULL 语义、COALESCE、IFNULL、NULLIF、IF 和 CASE 的选择。
tags:
  - MySQL
  - Function
  - "NULL"
category: MySQL
---

# MySQL NULL 与流程控制函数

## NULL 不是 0 或空字符串

`NULL` 表示未知、缺失或不适用。它参与普通比较时结果通常是“未知”，因此不要写 `column = NULL`，应使用 `IS NULL` 或 `IS NOT NULL`。

## 函数对比

| 函数 | 用法 | 适合场景 |
| :--- | :--- | :--- |
| `COALESCE(a, b, ...)` | 返回第一个非 NULL 值 | 标准 SQL，多级兜底 |
| `IFNULL(a, b)` | a 为 NULL 时返回 b | MySQL 中简单的两值兜底 |
| `NULLIF(a, b)` | 相等返回 NULL，否则返回 a | 把特殊值转换为 NULL，避免除零 |
| `IF(condition, yes, no)` | 条件二选一 | 简单分支 |
| `CASE` | 多条件分支 | 复杂分支和跨数据库 SQL |

```sql
SELECT
    COALESCE(nickname, username, 'anonymous') AS display_name,
    IFNULL(view_count, 0) AS safe_views,
    total_amount / NULLIF(item_count, 0) AS average_amount,
    CASE
        WHEN status = 1 THEN 'published'
        WHEN status = 0 THEN 'draft'
        ELSE 'unknown'
    END AS status_name
FROM article;
```

## 求值与类型

函数返回值的类型会受到各分支类型影响。尽量让 `IF`、`CASE` 的不同分支返回兼容类型，避免数字被隐式转换成字符串。表达式中出现 `NULL` 时，先确认是要保留“未知”，还是要显示为 0 或默认文字。

## 常见错误

- 使用 `= NULL` 判断空值。
- 无业务依据地把 `NULL` 全部替换为 0；“没有记录”和“数值为 0”可能是不同含义。
- 用 `IFNULL(indexed_column, value)` 筛选大表，导致索引使用受影响。
- `CASE` 条件顺序错误：匹配到第一个 `WHEN` 后就停止。
