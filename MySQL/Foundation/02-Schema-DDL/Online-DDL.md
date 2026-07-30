---
title: Online DDL
description: 面向初学者解释 MySQL 8.4 在线表结构变更,算法,锁,元数据锁和生产发布流程.
tags:
  - MySQL
  - DDL
  - Online-DDL
category: MySQL
mysql-version: "8.4 LTS"
---

# Online DDL

Online DDL 是让部分表结构变更在业务仍读写时完成的能力.它的准确含义是"尽量减少阻塞和停机",不是"完全不加锁"或"任何变更都在线".是否在线取决于 MySQL 版本,存储引擎,表定义,具体操作和服务器资源.

## 1. 三个关键词

| 关键词 | 作用 | 如何理解 |
| :--- | :--- | :--- |
| `ALGORITHM` | 选择变更实现方式 | `INSTANT` 通常只改元数据;`INPLACE` 由引擎处理;`COPY` 复制整表,成本最高 |
| `LOCK` | 允许的并发级别 | `NONE` 尽量允许读写,`SHARED` 或 `EXCLUSIVE` 会限制并发 |
| MDL | 保护对象定义 | 所有 DDL 都需要 metadata lock,长事务仍可能让在线变更等待 |

算法和锁级别是约束条件,不是性能保证.指定不兼容的组合时,MySQL 可能报错,而不是自动选择你想要的方案.

## 2. 示例:尝试即时加列

```sql

ALTER TABLE articles
    ADD COLUMN summary VARCHAR(255) NULL,
    ALGORITHM = INSTANT,
    LOCK = NONE;
```

如果目标版本或表定义不支持 `INSTANT`,应让语句失败并重新评估,而不是无意中降级成代价更高的算法.先在相同版本,相同表结构和接近真实数据量的环境演练.

## 3. 为什么仍可能阻塞

1. DDL 开始前需要获取 MDL;已有长事务可能持有共享元数据锁.
2. DDL 进入等待后,后续访问同一对象的请求可能排在它后面.
3. 变更完成时需要短暂的排他 MDL 完成提交.
4. `INPLACE` 或 `COPY` 可能消耗 CPU,磁盘,I/O 和 buffer pool,间接拖慢业务.
5. 复制环境还会把 DDL 传播到副本,造成复制延迟或顺序等待.

所以"在线"描述的是业务影响的目标,不是没有任何代价.

## 4. 执行前检查

```sql

SHOW CREATE TABLE articles;
SELECT TABLE_ROWS, DATA_LENGTH, INDEX_LENGTH
FROM information_schema.TABLES
WHERE TABLE_SCHEMA = DATABASE()
  AND TABLE_NAME = 'articles';

-- 观察当前会话与长事务;具体字段依版本和权限而异
SELECT trx_id, trx_started, trx_mysql_thread_id
FROM information_schema.INNODB_TRX
ORDER BY trx_started;
```

同时确认磁盘余量,复制状态,峰值流量,备份窗口和应用兼容版本.不要只根据表行数判断成本,行宽,索引数量和操作类型同样重要.

## 5. 常见操作的经验分类

| 操作 | 可能的成本 | 需要重点确认 |
| :--- | :--- | :--- |
| 某些新增列 | 可能 `INSTANT` | 列位置,默认值,版本限制 |
| 新建普通索引 | 常由引擎在线构建 | 磁盘,I/O,写入延迟,MDL |
| 修改列类型或字符集 | 可能重建或复制表 | 数据转换,索引长度,锁和回滚 |
| 删除列 | 常需要重组数据 | 数据备份,应用兼容,磁盘与耗时 |
| 添加唯一键 | 需要扫描并构建索引 | 重复数据,写入冲突,资源峰值 |

这张表只是风险预筛选,最终以目标 MySQL 版本的官方支持矩阵和实际演练为准.

## 6. 生产发布流程

1. 明确变更目的,受影响对象和成功标准.
2. 在同版本环境用真实结构与近似数据量演练,记录算法,耗时,锁等待和资源曲线.
3. 先部署兼容的新旧应用代码,例如先让读路径接受新列,再启用写入.
4. 在低峰期执行,设置会话级超时和可观测告警.
5. 监控 MDL,线程,延迟,错误率,复制和磁盘空间.
6. 变更后用 `SHOW CREATE TABLE`,抽样数据和应用指标复核.
7. 准备反向迁移或向前修复;不要把普通 `ROLLBACK` 当作 DDL 回滚.

## 7. 排障思路

DDL 长时间不动时,先确认它是在等待 MDL,等待 I/O,还是执行了重建:

```sql

SHOW PROCESSLIST;

SELECT *
FROM performance_schema.metadata_locks
WHERE OBJECT_SCHEMA = DATABASE()
  AND OBJECT_NAME = 'articles';
```

不同版本和权限下可见字段会不同.找到阻塞会话后,先联系事务所有者安全结束事务,再决定是否取消 DDL;直接 `KILL` 可能造成应用错误或回滚成本.

## 8. 常见误区

- 把 `LOCK=NONE` 写成"不会阻塞",忽略 MDL 和提交阶段.
- 只在空表测试,生产大表却使用同一算法假设.
- DDL 等待时反复提交相同语句,制造更多排队请求.
- 改字段时忘记应用兼容窗口,先删列再发布旧代码.
- 未检查副本与磁盘,把主库成功误认为整个集群安全.

## 9. 相关主题

- [DDL 数据定义语言百科](DDL-数据定义语言百科.md)
- [ALTER TABLE 修改表](ALTER-TABLE-修改表.md)
- [锁等待排查 Lock Wait](锁等待排查-Lock-Wait.md)
- [数据迁移 Data Migration](数据迁移-Data-Migration.md)

## 10. 官方参考

- [MySQL 8.4 Reference Manual: InnoDB Online DDL Operations](https://dev.mysql.com/doc/refman/8.4/en/innodb-online-ddl-operations.html)
- [MySQL 8.4 Reference Manual: Online DDL Performance and Concurrency](https://dev.mysql.com/doc/refman/8.4/en/innodb-online-ddl-performance.html)
