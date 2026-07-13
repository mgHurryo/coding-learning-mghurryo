---
title: change buffer
description: 说明 InnoDB change buffer 对非唯一二级索引写入的缓冲作用和适用边界。
tags:
  - MySQL
  - InnoDB
category: MySQL
---

# change buffer

## 速览

change buffer 用于缓存对不在 Buffer Pool 中的非唯一二级索引页的变更，减少随机读写。它适合写多读少、二级索引页不在内存中的场景。

## 核心机制

插入或更新非唯一二级索引时，如果目标索引页不在 Buffer Pool，InnoDB 可以先把变更记录到 change buffer，等该页后续被读入或后台合并时再应用。唯一索引通常不能这样做，因为必须立即检查唯一性。

## SQL/配置示例

```sql
SHOW VARIABLES LIKE 'innodb_change_buffering';
SHOW ENGINE INNODB STATUS;
```

## 项目落地

大量导入或写入时，过多二级索引会增加维护成本。change buffer 能缓解部分随机 I/O，但不能替代合理索引设计。

## 常见错误

不要以为 change buffer 对所有索引都生效；唯一索引、热点页已在 Buffer Pool 的场景收益有限。

## 相关主题

- [Secondary Index 二级索引](../05-Indexing/Secondary-Index-二级索引.md)
- [Buffer Pool](Buffer-Pool.md)
