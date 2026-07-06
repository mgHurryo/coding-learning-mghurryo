---
title: B+Tree 索引结构
description: 说明 InnoDB B+Tree 索引的页、层级、范围扫描和为什么它适合数据库查询。
tags:
  - MySQL
  - Index
  - B+Tree
category: MySQL
---

# B+Tree 索引结构

## 速览

B+Tree 是 InnoDB 常用索引结构。它通过有序页和多层目录把查找成本控制在很少的随机 I/O 内，同时天然支持范围扫描和排序读取。

## 核心机制

B+Tree 的非叶子节点保存键值和子页指针，叶子节点保存真正的索引记录。InnoDB 页通常以 16KB 为单位组织数据，节点扇出较大，因此几层树就能覆盖大量记录。等值查询从根页一路定位到叶子页；范围查询先定位起点，再沿叶子页链表顺序扫描。

## SQL/配置示例

```sql
CREATE INDEX idx_article_user_time ON article(user_id, create_time);
SELECT id, title FROM article WHERE user_id = 10 ORDER BY create_time DESC LIMIT 20;
```

## 项目落地

设计索引时要围绕高频 WHERE、ORDER BY、JOIN 条件，而不是看到字段就建索引。B+Tree 对等值、范围、前缀排序友好，对低选择性字段、左模糊和函数包裹字段帮助有限。

## 常见错误

不要把 B+Tree 想成每个字段都能随便跳转；联合索引仍受最左前缀约束。不要忽略页分裂、随机主键和过多索引带来的写入成本。

## 面试追问

- 为什么 MySQL 索引常用 B+Tree 而不是普通二叉树？
- B+Tree 为什么适合范围查询？
- 聚簇索引和二级索引的叶子节点分别存什么？

## 排障/边界

用 `EXPLAIN` 看 `type`、`key`、`rows` 和 `Extra`，验证查询是否真的利用 B+Tree 缩小扫描范围。若 `rows` 估算很大，要重新评估选择性和索引字段顺序。

## 相关主题

- [[MySQL/05-Indexing/Primary-Index-主键索引|Primary Index 主键索引]]
- [[MySQL/05-Indexing/Secondary-Index-二级索引|Secondary Index 二级索引]]
- [[MySQL/05-Indexing/Composite-Index-联合索引|Composite Index 联合索引]]
