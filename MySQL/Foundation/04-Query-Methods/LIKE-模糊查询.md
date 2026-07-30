---
title: LIKE 模糊查询
description: 讲解 LIKE 的百分号与下划线通配符、转义、排序规则、索引边界和全文检索替代方案。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# LIKE 模糊查询

## 方法定位

`LIKE` 按字符模式匹配字符串，适合前缀、后缀、包含和固定长度模式。

## 两个通配符

| 通配符 | 含义 |
| :---: | :--- |
| `%` | 匹配零个或任意多个字符 |
| `_` | 恰好匹配一个字符 |

```sql

-- 以 MySQL 开头
SELECT id, title
FROM article
WHERE title LIKE 'MySQL%';

-- 包含 MySQL
SELECT id, title
FROM article
WHERE title LIKE '%MySQL%';

-- 标题恰好两个字符
SELECT id, title
FROM article
WHERE title LIKE '__';
```

通配符只在 `LIKE` 模式中有特殊含义。

## 匹配字面量通配符

搜索文本本身可能包含 `%` 或 `_` 时，要进行转义。可显式声明转义字符：

```sql

SELECT id, title
FROM article
WHERE title LIKE '%!%%' ESCAPE '!';
```

这里 `!%` 表示字面量百分号。应用中还应先转义用户输入里的转义字符本身，再转义通配符，最后通过参数绑定传入。

## 大小写与排序规则

是否区分大小写主要由列的 collation 决定，而不是 `LIKE` 本身。许多 `_ci` 排序规则不区分大小写，`_bin` 或部分 `_cs` 规则区分。不要仅靠在 SQL 中改大小写猜测行为，先检查字段排序规则。

## NULL 行为

`NULL LIKE 'x%'` 的结果是未知，因此不会被 `WHERE` 保留。需要把 NULL 当空字符串时可写 `COALESCE(column, '')`，但对列套函数可能影响索引，应确认这正是业务语义。

## 索引边界

- `LIKE 'MySQL%'` 有固定前缀，普通 B+Tree 索引可能用于范围扫描。
- `LIKE '%MySQL'` 和 `LIKE '%MySQL%'` 以通配符开头，通常无法利用普通索引直接定位。
- 字段的字符集、排序规则和索引前缀长度也会影响计划。
- 数据量大且需要自然语言搜索时，考虑 `FULLTEXT` 或专业搜索引擎。

是否使用索引仍应通过 `EXPLAIN` 验证。

## LIKE 与 REGEXP

`LIKE` 适合简单模式；`REGEXP` 能表达更复杂的正则，但通常计算成本更高，也更难利用普通索引。能用清晰前缀条件解决时，不要为了“灵活”改用正则。

## 常见错误

- 把 `_` 当作普通下划线。
- 用户输入未经转义，使其意外变成通配模式。
- 用双引号依赖特定 SQL 模式；字符串推荐单引号。
- 在大表上使用左通配搜索，却期望普通索引高效。
- 误以为 `LIKE` 一定区分或一定不区分大小写。

## 小练习

1. 查询以 `MySQL` 开头的标题。
2. 查询第二个字符为 `A` 的三字符标题。
3. 查询包含字面量 `%` 的标题。
4. 比较前缀匹配与包含匹配的执行计划。

## 相关主题

- [WHERE 条件过滤](WHERE-条件过滤.md)
- [Charset 字符集与排序规则](Charset-字符集与排序规则.md)
- [Index Failure 索引失效场景](Index-Failure-索引失效场景.md)
