---
title: 缓存一致性 Cache Consistency
description: 说明 MySQL 与缓存协作时的 cache aside、失效、双写和延迟一致性问题。
tags:
  - MySQL
  - Architecture
  - Cache
category: MySQL
---

# 缓存一致性 Cache Consistency

## 速览

缓存能降低数据库读压力，但会引入一致性问题。常见策略是 cache aside：读缓存未命中查库再写缓存，写数据后删除或更新缓存。

## 核心机制

数据库和缓存不是同一个事务系统，双写天然存在失败窗口。删除缓存通常比更新缓存更稳，但仍可能遇到并发读写导致旧值回填，需要结合过期时间、延迟双删、版本号或消息重试。

## SQL/配置示例

```text
read: cache miss -> SELECT db -> set cache
write: UPDATE db -> delete cache
```

## 项目落地

强一致数据不要只依赖缓存。用户资料、文章详情等可接受短暂不一致；余额、权限、订单状态要谨慎使用缓存，并保留数据库校验路径。

## 常见错误

不要先删缓存再写数据库而不考虑并发回填；不要给缓存设置无限 TTL；不要把缓存当唯一事实来源。

## 相关主题

- [数据库边界与业务幂等](数据库边界与业务幂等.md)
- [INSERT ON DUPLICATE KEY UPDATE](../03-SQL-DML/INSERT-ON-DUPLICATE-KEY-UPDATE.md)
