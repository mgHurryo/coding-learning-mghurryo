---
title: MySQL JSON 函数
description: 面向初学者讲解 MySQL 8 JSON 文档的读取、修改、判断、数组展开和索引注意事项。
tags:
  - MySQL
  - Function
  - JSON
category: MySQL
---

# MySQL JSON 函数

## JSON 路径

MySQL 用 `$` 表示文档根，`.name` 访问对象成员，`[0]` 访问数组第一个元素：

```sql
SELECT JSON_EXTRACT(profile, '$.name') AS name
FROM user_profile;
```

## 常用函数表

| 函数 | 作用 |
| :--- | :--- |
| `JSON_EXTRACT(doc, path)` | 按路径读取值；`->` 是常见简写 |
| `JSON_UNQUOTE(value)` | 去掉 JSON 字符串外层引号；`->>` 可组合读取 |
| `JSON_SET(doc, path, value)` | 存在则更新，不存在则新增 |
| `JSON_INSERT(doc, path, value)` | 只在路径不存在时新增 |
| `JSON_REPLACE(doc, path, value)` | 只更新已存在路径 |
| `JSON_REMOVE(doc, path)` | 删除指定路径 |
| `JSON_CONTAINS(doc, candidate[, path])` | 判断是否包含 JSON 值 |
| `JSON_CONTAINS_PATH(doc, 'one'|'all', path...)` | 判断路径是否存在 |
| `JSON_LENGTH(doc[, path])` | 返回对象成员数或数组长度 |
| `JSON_KEYS(doc[, path])` | 返回对象键名数组 |
| `JSON_VALID(value)` | 判断文本是否是合法 JSON |
| `JSON_TABLE` | 把 JSON 展开为关系表，可用于连接查询 |

## 读取和修改

```sql
SELECT
    profile->>'$.name' AS name,
    profile->'$.roles' AS roles,
    JSON_LENGTH(profile, '$.roles') AS role_count
FROM user_profile;

UPDATE user_profile
SET profile = JSON_SET(profile, '$.active', true)
WHERE id = 1;
```

读取字符串时优先使用 `->>`，否则 `JSON_EXTRACT` 可能返回带 JSON 引号的结果。

## 设计和性能提示

- JSON 适合结构变化较多、整体读取或少量路径检索的属性；高频过滤、排序和连接字段优先使用普通列。
- 对 JSON 路径频繁查询时，可评估生成列、函数索引或多值索引。
- 修改函数返回新文档表达式，必须放入 `UPDATE` 才会持久化。
- `JSON_CONTAINS` 的对象、数组和标量匹配规则不同，先用小数据验证语义。
- JSON 函数和 `JSON_TABLE` 的具体行为以 MySQL 8.4 手册为准。
