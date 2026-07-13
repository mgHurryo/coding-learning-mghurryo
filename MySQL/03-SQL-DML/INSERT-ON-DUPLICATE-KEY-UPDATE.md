---
title: INSERT ON DUPLICATE KEY UPDATE
description: 用于插入时遇到唯一键或主键冲突则转为更新，常用于 upsert。
tags:
  - MySQL
  - DML
category: MySQL
---

# INSERT ON DUPLICATE KEY UPDATE

## 方法定位

用于插入时遇到唯一键或主键冲突则转为更新，常用于 upsert。

## 基本语法

```sql
INSERT INTO user_stat (user_id, login_count) VALUES (1, 1) ON DUPLICATE KEY UPDATE login_count = login_count + 1;
```

## 示例场景

记录用户登录次数时，首次插入，后续登录更新计数。

## 使用边界

适合按唯一键幂等写入；复杂业务状态变更仍应放在事务和 Service 逻辑中。

## 常见错误

不要在 update 部分做不清晰的覆盖；不要忽略并发下的业务含义。

## 调优提示

依赖唯一索引定位冲突，唯一键设计直接影响 upsert 成本。

## 相关主题

- [Unique Index 唯一索引](../05-Indexing/Unique-Index-唯一索引.md)
- [幂等接口设计](../../Network/HTTP/Guide/幂等接口设计.md)


