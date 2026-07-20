---
title: LIMIT 分页
description: 讲解 LIMIT 行数限制、OFFSET 深分页问题、稳定排序、游标分页和总数查询。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# LIMIT 分页

## 方法定位

`LIMIT` 限制返回行数，常用于列表分页、预览和保护查询结果规模。

## 两种写法

```sql

-- 跳过 20 行，返回 10 行
SELECT id, title
FROM article
ORDER BY create_time DESC, id DESC
LIMIT 10 OFFSET 20;

-- MySQL 简写：第一个数是 offset，第二个数是 row_count
SELECT id, title
FROM article
ORDER BY create_time DESC, id DESC
LIMIT 20, 10;
```

初学者更推荐 `LIMIT 10 OFFSET 20`，参数含义更直观。

## 页码转 OFFSET

每页 `page_size` 条，第 `page_number` 页从 1 开始：

```text

offset = (page_number - 1) * page_size
```

例如每页 20 条，第 3 页的 offset 是 40。

## 必须配合稳定排序

```sql

SELECT id, title, create_time
FROM article
WHERE status = 1
ORDER BY create_time DESC, id DESC
LIMIT 20 OFFSET 40;
```

若只按可能重复的 `create_time` 排序，同时间行在不同请求中顺序可能变化。加入唯一 `id` 作为最后排序键。

即便顺序稳定，分页期间有新数据插入或删除，OFFSET 分页仍可能重复或跳过行。强一致浏览需要快照、固定边界或业务层策略。

## 深分页为什么慢

`LIMIT 100000, 20` 通常仍需找到并跳过前 100000 行，若还涉及回表或排序，代价更高。LIMIT 只减少返回给客户端的行数，不保证前置工作少。

## 游标分页 / Seek Pagination

已知上一页最后一行的 `create_time` 和 `id`：

```sql

SELECT id, title, create_time
FROM article
WHERE status = 1
  AND (
        create_time < ?
        OR (create_time = ? AND id < ?)
      )
ORDER BY create_time DESC, id DESC
LIMIT 20;
```

也可在 MySQL 中使用行构造器比较，但展开条件更容易解释和跨场景调整。游标分页适合连续向后浏览，性能通常不随页数线性恶化；缺点是不方便直接跳到任意页。

## 只取一行也要定义规则

```sql

SELECT id, title
FROM article
WHERE user_id = 1
ORDER BY create_time DESC, id DESC
LIMIT 1;
```

这明确表示“最新一篇”。没有 `ORDER BY` 的 `LIMIT 1` 只是任意一行。

## 总数查询

列表数据与总数通常是两条查询：

```sql

SELECT COUNT(*)
FROM article
WHERE status = 1;
```

大表精确计数可能昂贵。产品若只需“是否有下一页”，可多取一条记录；若允许近似值或缓存，应明确一致性要求。

## 参数安全

`LIMIT` 和 `OFFSET` 应校验为非负整数，并设置合理的最大页大小。不同驱动对 LIMIT 占位符支持方式可能不同，但都不应直接接受未校验字符串拼接。

## 常见错误

- 没有 `ORDER BY` 就分页。
- 排序键不唯一。
- 把 `LIMIT` 当成避免全表扫描的保证。
- 用超大 OFFSET 支持无限翻页。
- 页大小不设上限。
- `LIMIT 20, 10` 中两个参数顺序记反。

## 相关主题

- [ORDER BY 排序](ORDER-BY-排序.md)
- [LIMIT 深分页调优](../08-Performance-Diagnostics/LIMIT-深分页调优.md)
- [Composite Index 联合索引](../05-Indexing/Composite-Index-联合索引.md)
