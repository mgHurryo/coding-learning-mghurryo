---
title: Window Functions 窗口函数
description: 面向初学者讲解 MySQL 8 窗口函数的分区、排序、窗口帧、排名、累计和组内 Top N。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# Window Functions 窗口函数

## 方法定位

窗口函数对一组相关行进行计算，但不会像 `GROUP BY` 那样把多行压缩成一行。MySQL 8.0+ 支持窗口函数。

```sql

SELECT
    id,
    category_id,
    view_count,
    ROW_NUMBER() OVER (
        PARTITION BY category_id
        ORDER BY view_count DESC, id ASC
    ) AS ranking
FROM article;
```

每篇文章仍保留一行，同时获得在所属分类内的排名。

## OVER 子句

```sql

function_name(...) OVER (
    [PARTITION BY partition_expression]
    [ORDER BY sort_expression]
    [frame_clause]
)
```

- `PARTITION BY`：把结果划分为互不影响的窗口分区，类似“按谁分组”，但不折叠行。
- `ORDER BY`：定义分区内顺序。
- 窗口帧：在当前行附近选取参与计算的行范围。

省略 `PARTITION BY` 时，全部结果行属于一个分区。

## 排名函数

| 函数 | 并列时的行为 |
| :--- | :--- |
| `ROW_NUMBER()` | 每行序号唯一，不保留并列 |
| `RANK()` | 并列同名次，后续名次跳号 |
| `DENSE_RANK()` | 并列同名次，后续名次不跳号 |

若分数为 `100, 100, 90`：

- `ROW_NUMBER`：1, 2, 3
- `RANK`：1, 1, 3
- `DENSE_RANK`：1, 1, 2

排名的 `ORDER BY` 最好增加唯一字段作为最终排序键，确保结果稳定。

## 组内 Top N

窗口函数结果通常不能直接在同一层 `WHERE` 中过滤，因为 `WHERE` 逻辑上先执行。使用 CTE 或派生表：

```sql

WITH ranked_article AS (
    SELECT
        id,
        category_id,
        title,
        view_count,
        ROW_NUMBER() OVER (
            PARTITION BY category_id
            ORDER BY view_count DESC, id ASC
        ) AS rn
    FROM article
    WHERE status = 1
)
SELECT id, category_id, title, view_count
FROM ranked_article
WHERE rn <= 3
ORDER BY category_id, rn;
```

## 聚合窗口函数

`SUM`、`AVG`、`COUNT`、`MIN`、`MAX` 也可作为窗口函数：

```sql

SELECT
    id,
    user_id,
    create_time,
    view_count,
    SUM(view_count) OVER (
        PARTITION BY user_id
        ORDER BY create_time, id
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_views
FROM article;
```

这会计算每名用户按时间排列的累计阅读量。

## ROWS 与 RANGE

窗口帧决定当前计算包含哪些行：

```sql

ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
```

- `ROWS` 按物理行位置定义范围。
- `RANGE` 按排序值的同值组定义范围，相同排序值可能一起进入窗口。
- 做逐行累计时，显式使用 `ROWS ...` 往往更符合直觉。
- 某些带 `ORDER BY` 的窗口聚合有默认窗口帧；不要在不了解默认值时省略。

## 前后行函数

```sql

SELECT
    id,
    user_id,
    create_time,
    view_count,
    LAG(view_count) OVER (
        PARTITION BY user_id
        ORDER BY create_time, id
    ) AS previous_views,
    LEAD(view_count) OVER (
        PARTITION BY user_id
        ORDER BY create_time, id
    ) AS next_views
FROM article;
```

- `LAG` 读取前一行。
- `LEAD` 读取后一行。
- `FIRST_VALUE` / `LAST_VALUE` 读取窗口帧中的首值 / 尾值；`LAST_VALUE` 尤其要注意默认窗口帧。

## GROUP BY 与窗口函数对比

| 需求 | 推荐 |
| :--- | :--- |
| 每个分类只返回一行统计 | `GROUP BY category_id` |
| 保留每篇文章并显示分类总数 | `COUNT(*) OVER (PARTITION BY category_id)` |
| 每组 Top N | `ROW_NUMBER` / `RANK` |
| 累计值、移动平均、环比 | 窗口函数 |

聚合与窗口函数可以组合，但应清楚每一层查询的数据粒度。

## 性能基础

- 分区和排序可能使用临时表或排序。
- 大分区的内存与 I/O 成本较高。
- `WHERE` 先过滤不需要的行，可减少窗口输入。
- 索引可能帮助提供过滤和部分顺序，但不保证消除窗口计算成本。
- 使用 `EXPLAIN ANALYZE` 验证实际行数和耗时。

## 常见错误

- 在同一层 `WHERE` 中直接引用窗口函数别名。
- 排名只按非唯一列排序，导致并列行次序不稳定。
- 混淆 `RANK` 与 `ROW_NUMBER`。
- 忽略默认窗口帧，导致累计或 `LAST_VALUE` 结果出乎预期。
- 用窗口函数替代本应由简单聚合完成的查询。

## 练习

1. 计算每个分类内文章的阅读量排名。
2. 取每名用户最新发布的 1 篇文章。
3. 计算每名用户按发布时间累计的阅读量。
4. 使用 `LAG` 计算相邻文章阅读量的差值。

## 相关主题

- [GROUP BY 分组](GROUP-BY-分组.md)
- [CTE 公用表表达式](CTE-公用表表达式.md)
- [ORDER BY 排序](ORDER-BY-排序.md)
