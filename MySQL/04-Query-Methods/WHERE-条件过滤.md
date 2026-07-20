---
title: WHERE 条件过滤
description: 系统讲解 WHERE 的比较、逻辑组合、NULL 三值逻辑、日期条件、安全边界与索引友好写法。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# WHERE 条件过滤

## 方法定位

`WHERE` 在分组和输出之前筛选原始行。它也用于 `UPDATE` 和 `DELETE`，因此既是查询工具，也是写操作的安全边界。

## 比较条件

```sql

SELECT id, title
FROM article
WHERE status = 1;

SELECT id, title
FROM article
WHERE user_id <> 1;

SELECT id, title
FROM article
WHERE view_count >= 1000;
```

常用比较符：`=`、`<>` / `!=`、`>`、`>=`、`<`、`<=`。

## 组合条件

```sql

SELECT id, title
FROM article
WHERE user_id = 1
  AND status = 1;

SELECT id, title
FROM article
WHERE (status = 0 OR status = 1)
  AND view_count >= 100;
```

`AND` 优先级高于 `OR`。混用时用括号明确业务逻辑，不要要求读者背优先级。

## 常见谓词

```sql

-- 匹配多个候选用户
SELECT id, title
FROM article
WHERE user_id IN (1, 2, 3);

-- 闭区间 1 到 3
SELECT id, title
FROM article
WHERE user_id BETWEEN 1 AND 3;

-- 前缀匹配
SELECT id, title
FROM article
WHERE title LIKE 'MySQL%';

-- 空值判断
SELECT id
FROM article
WHERE category_id IS NULL;
```

分别见 [IN 多值匹配](IN-多值匹配.md)、[BETWEEN 范围查询](BETWEEN-范围查询.md)、[LIKE 模糊查询](LIKE-模糊查询.md) 和 [IS NULL 空值判断](IS-NULL-空值判断.md)。

## NULL 与三值逻辑

不要写 `column = NULL` 或 `column <> NULL`。普通比较遇到 NULL 会得到“未知”，而 `WHERE` 只保留为真的行。

```sql

WHERE category_id IS NULL
WHERE category_id IS NOT NULL
```

`NOT (category_id = 1)` 也不会保留 NULL 行；需要时显式写 `category_id <> 1 OR category_id IS NULL`。

## 时间范围推荐左闭右开

查询 2026-07-20 全天：

```sql

SELECT id, title
FROM article
WHERE create_time >= '2026-07-20 00:00:00'
  AND create_time <  '2026-07-21 00:00:00';
```

相比 `DATE(create_time) = '2026-07-20'`，范围写法通常更利于使用 `create_time` 的普通索引，也不会遗漏带小数秒的数据。

## WHERE 与 HAVING

`WHERE` 过滤明细行；`HAVING` 过滤聚合后的分组：

```sql

SELECT user_id, COUNT(*) AS article_count
FROM article
WHERE status = 1
GROUP BY user_id
HAVING COUNT(*) >= 10;
```

## 索引友好写法

- 参数类型与列类型一致，避免隐式转换。
- 不要无故对索引列套函数或计算。
- 前缀匹配 `LIKE 'MySQL%'` 可能利用索引，左通配 `LIKE '%MySQL'` 通常难以使用普通 B+Tree 定位。
- 联合索引应依据整体过滤、排序和选择性设计，不是把所有 WHERE 列都建单列索引。
- 优化器可能不使用低选择性条件的索引，是否命中以执行计划为准。

## 安全边界

在多租户或逻辑删除系统中，查询条件常必须包含租户与删除状态：

```sql

SELECT id, title
FROM article
WHERE tenant_id = ?
  AND deleted = 0
  AND id = ?;
```

占位符由应用参数绑定。不要把用户输入直接拼进 SQL。

## 常见错误

- 忘记括号导致 `AND` / `OR` 逻辑偏差。
- 用 `= NULL`。
- 日期列使用函数后再比较。
- 数值列与字符串参数混用。
- 忘记租户、权限或逻辑删除条件。
- 将聚合条件放在 `WHERE`。
- 误以为条件书写顺序就是物理执行顺序。

## 小练习

1. 查询用户 1 或 2 的已发布文章。
2. 查询标题以 MySQL 开头、阅读量至少 100 的文章。
3. 查询某一天的数据，并解释为什么使用左闭右开范围。
4. 写出“分类不是 1，且也允许分类为空”的条件。

## 相关主题

- [运算符与表达式](运算符与表达式.md)
- [SELECT 逻辑执行顺序](SELECT-逻辑执行顺序.md)
- [WHERE 调优](../08-Performance-Diagnostics/WHERE-调优.md)
- [Composite Index 联合索引](../05-Indexing/Composite-Index-联合索引.md)
