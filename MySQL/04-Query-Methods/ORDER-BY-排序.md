---
title: ORDER BY 排序
description: 讲解多列排序、ASC 与 DESC、NULL 顺序、表达式排序、稳定结果和索引排序。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# ORDER BY 排序

## 方法定位

`ORDER BY` 定义查询结果顺序。关系表没有默认顺序；没有 `ORDER BY` 的结果即使当前看起来稳定，也可能随执行计划或数据变化。

## 基本语法

```sql

SELECT id, title, create_time
FROM article
ORDER BY create_time DESC;
```

- `ASC`：升序，默认值。
- `DESC`：降序。
- 每个排序表达式都可单独指定方向。

## 多列排序

```sql

SELECT id, title, view_count, create_time
FROM article
ORDER BY view_count DESC, create_time DESC, id DESC;
```

先按阅读量降序；阅读量相同时再按时间；仍相同时按 ID。排序键从左到右依次生效。

## 稳定排序

分页查询必须有确定顺序。仅按 `create_time` 排序时，多行可能同一时间，跨页读取会重复或遗漏。加入唯一键作为最后的决胜条件：

```sql

ORDER BY create_time DESC, id DESC
```

“稳定”不是指数据变化时分页快照不变，而是指给定同一数据集时行顺序能唯一确定。

## 按别名或表达式排序

```sql

SELECT id, view_count * 2 AS score
FROM article
ORDER BY score DESC, id DESC;
```

`ORDER BY` 可以使用输出别名。也可使用列序号如 `ORDER BY 2`，但列顺序变更会悄悄改变语义，不推荐生产代码使用。

## NULL 顺序

MySQL 升序时 NULL 通常在前，降序时通常在后。显式控制 NULL 在后：

```sql

SELECT id, category_id
FROM article
ORDER BY category_id IS NULL, category_id ASC;
```

布尔表达式对非 NULL 返回 0，对 NULL 返回 1，因此非 NULL 先出现。

## 自定义业务顺序

```sql

SELECT id, status
FROM article
ORDER BY
    CASE status
        WHEN 1 THEN 1
        WHEN 0 THEN 2
        ELSE 3
    END,
    id DESC;
```

复杂表达式排序可能需要额外计算和 filesort。

## 索引与 filesort

若索引的列顺序、方向和查询过滤条件与排序匹配，MySQL 可能按索引顺序读取，避免额外排序。否则执行计划的 `Extra` 可能出现 `Using filesort`。

`Using filesort` 不等于一定写磁盘，也不等于必然很慢；它表示不能直接靠索引顺序完成排序。应结合实际行数、内存、LIMIT 和耗时判断。

## 常见错误

- 依赖默认顺序。
- 分页排序键不唯一。
- 误以为多列排序是各列独立排序。
- 使用列序号排序，降低可维护性。
- 为满足一个低频排序创建冗余索引，却忽略写入成本。
- 看到 `Using filesort` 就盲目加索引。

## 相关主题

- [LIMIT 分页](LIMIT-分页.md)
- [CASE 条件表达式](CASE-条件表达式.md)
- [ORDER BY 调优](../08-Performance-Diagnostics/ORDER-BY-调优.md)
