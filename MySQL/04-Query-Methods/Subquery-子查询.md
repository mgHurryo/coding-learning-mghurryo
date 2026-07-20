---
title: Subquery 子查询
description: 系统讲解标量、列、行、表子查询，关联子查询、EXISTS、派生表、NULL 陷阱与优化思路。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# Subquery 子查询

## 方法定位

子查询是嵌套在另一条 SQL 中的查询。它可以返回单个值、一列、多列多行或一个临时结果集，并出现在 `SELECT`、`FROM`、`WHERE`、`HAVING` 等位置。

## 按返回形状分类

| 类型 | 返回形状 | 常见使用位置 |
| :--- | :--- | :--- |
| 标量子查询 | 一行一列 | 比较、输出表达式 |
| 列子查询 | 多行一列 | `IN`、`ANY`、`ALL` |
| 行子查询 | 一行多列 | 行构造器比较 |
| 表子查询 | 多行多列 | `FROM` 派生表、CTE |

理解返回形状能避免“子查询返回多行”的错误。

## 标量子查询

查询阅读量高于平均值的文章：

```sql

SELECT id, title, view_count
FROM article
WHERE view_count > (
    SELECT AVG(view_count)
    FROM article
);
```

括号内必须返回一个值。若返回多行，会报错；无行时标量结果通常为 NULL，外层比较得到未知。

## 列子查询

```sql

SELECT id, username
FROM user
WHERE id IN (
    SELECT user_id
    FROM article
    WHERE status = 1
);
```

子查询返回多行一列。`NOT IN` 必须特别注意 NULL，详见 [IN 多值匹配](IN-多值匹配.md)。

## 关联子查询

关联子查询引用外层查询列：

```sql

SELECT u.id, u.username
FROM user AS u
WHERE EXISTS (
    SELECT 1
    FROM article AS a
    WHERE a.user_id = u.id
      AND a.status = 1
);
```

语义上对当前外层用户判断关联文章是否存在。优化器可能将其改写为半连接，不应简单理解为必然逐行完整扫描。

## SELECT 列中的子查询

```sql

SELECT
    u.id,
    u.username,
    (
        SELECT COUNT(*)
        FROM article AS a
        WHERE a.user_id = u.id
    ) AS article_count
FROM user AS u;
```

写法直观，但外层行数大时应检查计划。也可以先按用户聚合文章，再左连接统计结果。

## FROM 中的派生表

```sql

SELECT stats.user_id, stats.article_count
FROM (
    SELECT user_id, COUNT(*) AS article_count
    FROM article
    WHERE status = 1
    GROUP BY user_id
) AS stats
WHERE stats.article_count >= 10;
```

MySQL 要求 `FROM` 中的派生表有别名。复杂嵌套可改用 [CTE 公用表表达式](CTE-公用表表达式.md) 提升可读性。

## ANY 与 ALL

```sql

-- 大于子查询返回值中的至少一个
WHERE view_count > ANY (SELECT view_count FROM article WHERE category_id = 1)

-- 大于子查询返回的所有值
WHERE view_count > ALL (SELECT view_count FROM article WHERE category_id = 1)
```

空集与 NULL 会影响语义，实际使用时应构造小数据验证。多数业务使用聚合极值更易读，例如与 `MIN` 或 `MAX` 比较，但二者在空集 / NULL 情况下不一定完全等价。

## 子查询、JOIN 与 CTE 如何选

- 只判断存在性：优先考虑 `EXISTS`。
- 需要右表列：通常使用 `JOIN`。
- 需要先聚合再组合：派生表或 CTE 很清楚。
- 单个全局统计值：标量子查询自然。
- 多层逻辑需要命名：CTE 更易维护。

它们不只是性能替代关系，也表达不同语义。最终计划由优化器决定，应实测。

## 常见错误

- 标量位置的子查询返回多行。
- `NOT IN` 子查询含 NULL。
- 派生表没有别名。
- 关联条件漏写，子查询变成与外层无关的全局判断。
- 用多层嵌套表达本可清楚连接的逻辑。
- 相信“子查询永远慢”或“JOIN 永远快”的绝对结论。

## 性能基础

- 子查询内部也应只选必要列并尽早过滤。
- 关联列应有合适索引。
- 检查优化器是否物化、合并、半连接改写。
- 使用 `EXPLAIN ANALYZE` 比较改写前后的实际行数与耗时，而不是只比较 SQL 长度。

## 练习

1. 查询阅读量高于全表平均值的文章。
2. 分别用 `IN`、`EXISTS` 和 `JOIN` 查询发布过文章的用户。
3. 查询没有文章的用户，验证 `NOT IN` 遇到 NULL 的差异。
4. 先统计每名用户的文章数，再筛选至少 10 篇的用户。

## 相关主题

- [EXISTS 存在性查询](EXISTS-存在性查询.md)
- [CTE 公用表表达式](CTE-公用表表达式.md)
- [JOIN 连接查询](JOIN-连接查询.md)
- [EXPLAIN 使用方法](../08-Performance-Diagnostics/EXPLAIN-使用方法.md)
