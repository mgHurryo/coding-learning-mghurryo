---
title: UNION 集合查询
description: 使用 UNION 与 UNION ALL 纵向合并查询结果，理解列对齐、去重、排序和性能差异。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# UNION 集合查询

## 方法定位

`JOIN` 横向组合列，`UNION` 纵向追加行。参与集合运算的每个查询必须返回相同数量的列，对应位置的数据类型应兼容。

## UNION 与 UNION ALL

```sql

SELECT id, username AS display_name, 'user' AS source_type
FROM user
UNION ALL
SELECT id, title AS display_name, 'article' AS source_type
FROM article;
```

| 写法 | 行为 | 性能 |
| :--- | :--- | :--- |
| `UNION` | 合并后去重 | 通常需要额外去重工作 |
| `UNION ALL` | 保留所有行，包括重复 | 通常更快，语义也更直接 |

只有业务明确要求去重时才使用 `UNION`。

## 列名与类型

最终列名由第一个 `SELECT` 决定：

```sql

SELECT id AS object_id, username AS object_name
FROM user
UNION ALL
SELECT id, title
FROM article;
```

第二个查询的 `id` 和 `title` 分别对齐 `object_id` 和 `object_name`。列的含义也应对齐，不能仅因类型相同就混在一起。

## 整体排序与分页

`ORDER BY` 和 `LIMIT` 放在最后，作用于合并后的完整结果：

```sql

SELECT id, create_time, 'user' AS source_type
FROM user
UNION ALL
SELECT id, create_time, 'article' AS source_type
FROM article
ORDER BY create_time DESC, id DESC
LIMIT 20;
```

需要限制单个分支时，使用括号包裹该分支，并确认限制符合业务语义。

## 模拟 FULL OUTER JOIN

MySQL 不支持原生 `FULL OUTER JOIN`。可以组合左连接结果和仅右侧未匹配结果：

```sql

SELECT a.id AS article_id, c.id AS category_id
FROM article AS a
LEFT JOIN category AS c ON c.id = a.category_id

UNION ALL

SELECT a.id AS article_id, c.id AS category_id
FROM category AS c
LEFT JOIN article AS a ON a.category_id = c.id
WHERE a.id IS NULL;
```

第二部分只返回右侧独有的行，避免重复已经匹配的行。

## 常见错误

- 两侧返回列数不同。
- 对应列含义或类型不兼容，导致隐式转换。
- 本来允许重复却使用 `UNION`，增加去重成本。
- 误以为每个分支的 `ORDER BY` 能决定最终顺序。
- 用 `UNION` 拼接本可由单个 `WHERE ... OR ...` 清楚表达的查询，或反过来在不同访问路径适合拆分时强行使用复杂 OR。

## 相关主题

- [JOIN 连接查询](JOIN-连接查询.md)
- [ORDER BY 排序](ORDER-BY-排序.md)
- [LIMIT 分页](LIMIT-分页.md)
