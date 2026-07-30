---
title: BETWEEN 范围查询
description: 讲解 BETWEEN 的闭区间语义、NOT BETWEEN、日期时间边界、字符串排序规则与范围索引。
tags:
  - MySQL
  - DQL
  - Query
category: MySQL
---

# BETWEEN 范围查询

## 方法定位

`BETWEEN lower AND upper` 判断值是否位于闭区间，包含上下界。

```sql

SELECT id, title
FROM article
WHERE view_count BETWEEN 100 AND 1000;
```

等价于：

```sql

WHERE view_count >= 100
  AND view_count <= 1000
```

上下界应按从小到大书写；MySQL 不会自动交换。

## NOT BETWEEN

```sql

SELECT id, title
FROM article
WHERE view_count NOT BETWEEN 100 AND 1000;
```

对于非 NULL 值，相当于小于 100 或大于 1000。NULL 仍会产生未知，不会自动包含。

## 日期时间陷阱

```sql

-- 只包含 2026-07-20 00:00:00 这一时刻，不代表完整的一天
WHERE create_time BETWEEN '2026-07-01' AND '2026-07-20'
```

日期字符串转换为当天零点，因此会遗漏结束日零点之后的数据。按自然日查询推荐左闭右开：

```sql

WHERE create_time >= '2026-07-01 00:00:00'
  AND create_time <  '2026-07-21 00:00:00'
```

这种写法对小数秒也安全。

## 字符串范围

字符串 `BETWEEN` 按字段的字符集与排序规则比较，并不等同于人类理解的“字母范围”。大小写、重音和语言规则会影响结果。需要精确边界时先确认 collation。

## NULL 与类型

- 任一操作数为 NULL 时，结果通常是未知。
- 参数类型应与字段类型一致，避免隐式转换。
- 时间参数应明确时区和精度，尤其是应用与数据库时区不一致时。

## 索引与范围条件

B+Tree 索引擅长范围扫描，`BETWEEN` 与 `>= / <=` 在语义等价时通常有类似机会使用索引。联合索引中某列进入范围后，后续列能否继续用于定位或仅用于过滤，需要结合具体条件与执行计划判断。

## 常见错误

- 忘记 `BETWEEN` 包含两端。
- 上下界颠倒。
- 用闭区间日期字符串表达完整结束日。
- 认为 NULL 会落在范围外并被 `NOT BETWEEN` 返回。
- 对字段先调用函数再做范围比较。

## 相关主题

- [WHERE 条件过滤](WHERE-条件过滤.md)
- [日期时间类型](日期.md)
- [Composite Index 联合索引](Composite-Index-联合索引.md)
