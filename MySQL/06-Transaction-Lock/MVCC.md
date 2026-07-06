---
title: MVCC
description: 说明 InnoDB 多版本并发控制、Read View、undo 版本链、快照读和当前读的区别。
tags:
  - MySQL
  - Transaction
  - MVCC
category: MySQL
---

# MVCC

## 速览

MVCC 让读写在很多场景下不用互相阻塞。普通一致性读读取符合 Read View 的历史版本，更新和加锁读则走当前读和锁机制。

## 核心机制

InnoDB 记录隐藏事务 id 和回滚指针。数据被更新时，旧版本进入 undo 版本链。快照读创建 Read View 后，会根据活跃事务列表和事务 id 判断哪个版本可见。RR 下事务内首次一致性读建立的 Read View 可复用，RC 下每条语句通常使用新的 Read View。

## SQL/配置示例

```sql
SELECT * FROM article WHERE id = 1;
SELECT * FROM article WHERE id = 1 FOR UPDATE;
```

## 项目落地

普通列表和详情读取通常受益于 MVCC；抢库存、状态流转、余额更新等场景不能只靠快照读，要使用条件更新或当前读加锁。

## 常见错误

不要把快照读和当前读混为一谈；不要以为 MVCC 不需要锁；不要让长事务长期持有旧 Read View。

## 面试追问

- Read View 判断可见性的核心规则是什么？
- RC 和 RR 下 Read View 有什么差异？
- MVCC 为什么依赖 undo log？

## 排障/边界

长事务会让 undo 历史版本无法及时清理。看到 undo 膨胀、history list 变长或快照读变慢，要先查长事务。

## 相关主题

- [[MySQL/99-Common-Concepts/当前读与快照读|当前读与快照读]]
- [[MySQL/07-InnoDB-Internals/undo-log|undo log]]
- [[MySQL/06-Transaction-Lock/Isolation-Level-隔离级别|Isolation Level 隔离级别]]
