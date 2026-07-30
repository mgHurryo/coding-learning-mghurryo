---
title: SELECT 基础查询
description: 面向初学者讲解 SELECT 的列选择、别名、表达式、DISTINCT、FROM 和查询书写规范。
tags:
  - MySQL
  - DQL
  - DML
category: MySQL
---

# SELECT 基础查询

## 方法定位

`SELECT` 从表或其他数据源读取数据，也能计算常量与表达式。它是 DQL 的核心。

## 最小语法

```sql

SELECT id, username, create_time
FROM user
WHERE id = 1;
```

- `SELECT` 后是要返回的列或表达式。
- `FROM` 后是数据来源。
- 分号表示语句结束。
- SQL 关键字不区分大小写，推荐大写以便阅读。

没有 `FROM` 也能计算表达式：

```sql

SELECT 1 + 1 AS result, CURRENT_TIMESTAMP AS query_time;
```

## 选择列

```sql

-- 明确列名：生产查询推荐
SELECT id, username
FROM user;

-- 所有列：仅适合临时探索
SELECT *
FROM user;
```

避免长期使用 `SELECT *`：

- 表新增列后，返回结构会悄悄变化。
- 读取不需要的大字段会增加网络、内存与 I/O。
- 更难利用覆盖索引。
- 连接查询中容易产生同名列和映射冲突。

## 别名

```sql

SELECT
    u.id AS user_id,
    u.username AS username,
    u.create_time AS registered_at
FROM user AS u;
```

`AS` 可省略，但初学阶段建议保留。别名用于结果展示和消除歧义，不会修改原字段名。中文显示名可以写成 `AS '用户名'`，但应用接口通常更适合稳定的英文别名。

## 表达式与常量

```sql

SELECT
    id,
    title,
    view_count,
    view_count * 2 AS doubled_views,
    'article' AS object_type
FROM article;
```

每个输出表达式应有清晰含义。复杂条件分支见 [CASE 条件表达式](CASE-条件表达式.md)。

## DISTINCT 去重

```sql

SELECT DISTINCT username
FROM user;

SELECT DISTINCT category_id, status
FROM article;
```

`DISTINCT` 对整个输出列组合去重。第二条不是分别对两列去重，而是保留唯一的 `(category_id, status)` 组合。

连接后出现多行时，先检查一对多关系和连接条件，不要习惯性加 `DISTINCT` 掩盖错误。

## 结果没有默认顺序

```sql

SELECT id, title
FROM article
ORDER BY create_time DESC, id DESC;
```

没有 `ORDER BY` 时，即使当前看起来按主键返回，也不应依赖这个顺序。分页尤其需要稳定且最好唯一的排序键。

## 完整查询示例

```sql

SELECT
    a.id,
    a.title,
    a.view_count,
    a.create_time
FROM article AS a
WHERE a.status = 1
  AND a.view_count >= 100
ORDER BY a.create_time DESC, a.id DESC
LIMIT 20;
```

## 初学者常见错误

- 使用 `==` 比较，SQL 应使用 `=`。
- 字符串不加引号，或把字符串双引号风格当成跨环境保证；推荐单引号。
- 认为 `DISTINCT` 只作用于紧跟它的第一列。
- 认为查询有默认排序。
- 在多表查询中不写列前缀。
- 直接拼接用户输入。应用应使用预编译参数绑定。

## 性能入门

- 只选择必要列。
- 使用 `WHERE` 限制行，避免无条件读取大表。
- 为高频过滤、连接、排序路径设计索引。
- `LIMIT` 限制返回量，但不一定减少前面的扫描与排序工作。
- 用 [EXPLAIN 使用方法](EXPLAIN-使用方法.md) 检查访问方式。

## 小练习

1. 查询所有启用用户的 `id` 和 `username`。
2. 查询文章表中出现过的分类 ID，要求去重。
3. 为列起稳定英文别名，并按创建时间与 ID 倒序返回 10 行。
4. 解释为什么 `SELECT DISTINCT category_id, status` 不是分别去重。

## 相关主题

- [DQL MOC](DQL-MOC.md)
- [SELECT 逻辑执行顺序](SELECT-逻辑执行顺序.md)
- [WHERE 条件过滤](WHERE-条件过滤.md)
- [SELECT 调优](SELECT-调优.md)
