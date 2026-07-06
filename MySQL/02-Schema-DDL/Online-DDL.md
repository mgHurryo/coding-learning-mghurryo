---
title: Online DDL
description: 说明 MySQL 8.4 中在线表结构变更的能力、锁级别、风险和上线策略。
tags:
  - MySQL
  - DDL
  - Online-DDL
category: MySQL
---

# Online DDL

## 速览

Online DDL 是让 `ALTER TABLE` 尽量在不长时间阻塞读写的情况下完成表结构变更的能力。它不是“无锁改表”，而是根据操作类型选择 `INSTANT`、`INPLACE` 或 `COPY` 等算法，并在元数据锁、执行阶段和提交阶段产生不同影响。

## 核心机制

MySQL 8.4 的 InnoDB 对部分操作支持 `ALGORITHM=INSTANT`，例如某些新增列场景，通常只改数据字典；`INPLACE` 会在引擎内部构建或调整结构；`COPY` 会复制整表，成本最高。所有 DDL 都需要 metadata lock，长事务或未提交查询可能阻塞 DDL，DDL 也可能反过来阻塞后续业务请求。

## SQL/配置示例

```sql
ALTER TABLE article
  ADD COLUMN summary VARCHAR(255) NULL,
  ALGORITHM=INSTANT,
  LOCK=NONE;
```

## 项目落地

生产发版前要先在同版本、同量级数据上演练 DDL，确认算法、锁级别、耗时和回滚方案。大表改字段类型、建索引、删列通常要放到低峰期，并配合灰度发布。

## 常见错误

不要把 Online DDL 理解为完全不影响业务；不要在长事务堆积时执行 DDL；不要在未确认算法的情况下直接改大表。

## 面试追问

- `INSTANT`、`INPLACE`、`COPY` 的成本差异是什么？
- 为什么 Online DDL 仍然可能被 metadata lock 卡住？
- 大表加索引前要检查哪些风险？

## 排障/边界

若 DDL 长时间不动，先查 metadata lock 和长事务，再决定 kill 阻塞会话还是回滚发布。涉及历史版本时，要确认目标 MySQL 版本是否支持对应算法。

## 相关主题

- [[MySQL/02-Schema-DDL/ALTER-TABLE-修改表|ALTER TABLE 修改表]]
- [[MySQL/06-Transaction-Lock/锁等待排查-Lock-Wait|锁等待排查 Lock Wait]]
- [[MySQL/10-Operations/数据迁移-Data-Migration|数据迁移 Data Migration]]

## 参考资料

- [MySQL 8.4 Reference Manual - Online DDL Operations](https://dev.mysql.com/doc/refman/8.4/en/innodb-online-ddl-operations.html)
