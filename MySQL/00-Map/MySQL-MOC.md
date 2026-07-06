---
title: MySQL MOC
description: MySQL 系统知识索引，按基础、SQL、索引、事务锁、InnoDB、性能排障、复制高可用、运维、架构扩展与 Java 持久层分层组织。
tags:
  - MySQL
  - SQL
  - MOC
category: MySQL
---

# MySQL MOC

> 主线版本以 MySQL 8.4 LTS 为准。学习目标是同时服务复习、面试追问和 Spring Boot / MyBatis 项目落地。目录名全部使用英文和 hyphen，文件名保留中英文混合，方便检索。

## 学习路线

1. 先读 [[MySQL/01-Foundations/Database-数据库概念|基础概念]]、[[MySQL/01-Foundations/Table-表|表]]、[[MySQL/01-Foundations/DataType-数据类型|数据类型]]，建立 schema、表、列和字符集意识。
2. 再读 [[MySQL/02-Schema-DDL/CREATE-TABLE-创建表|DDL]]、[[MySQL/03-SQL-DML/SELECT-基础查询|DML]]、[[MySQL/04-Query-Methods/WHERE-条件过滤|查询方法]]，把 SQL 写法练熟。
3. 接着读 [[MySQL/05-Indexing/B+Tree-索引结构|B+Tree]]、[[MySQL/05-Indexing/Composite-Index-联合索引|联合索引]]、[[MySQL/06-Transaction-Lock/MVCC|MVCC]]、[[MySQL/06-Transaction-Lock/Isolation-Level-隔离级别|隔离级别]]，理解性能和并发。
4. 然后读 [[MySQL/07-InnoDB-Internals/Buffer-Pool|Buffer Pool]]、[[MySQL/07-InnoDB-Internals/redo-log|redo log]]、[[MySQL/07-InnoDB-Internals/binlog-与-redo-log-协作|binlog 与 redo log 协作]]，串起 InnoDB 的可靠性机制。
5. 最后读 [[MySQL/08-Performance-Diagnostics/慢-SQL-排查流程|慢 SQL 排查流程]]、[[MySQL/09-Replication-HA/主从复制-Replication|主从复制]]、[[MySQL/10-Operations/备份恢复-Backup-Restore|备份恢复]]、[[MySQL/12-Java-Persistence/MyBatis-批量操作|MyBatis 批量操作]]，落到线上诊断与项目实践。

## 01-Foundations 基础

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/01-Foundations/Database-数据库概念|Database 数据库概念]] | schema、业务域与权限边界 |
| [[MySQL/01-Foundations/Table-表|Table 表]] | 表、行、列、约束与索引的容器 |
| [[MySQL/01-Foundations/Column-字段|Column 字段]] | 字段语义、类型、默认值与可空性 |
| [[MySQL/01-Foundations/DataType-数据类型|DataType 数据类型]] | 数值、字符串、时间、金额类型选择 |
| [[MySQL/01-Foundations/Charset-字符集与排序规则|Charset 字符集与排序规则]] | utf8mb4、collation、比较与排序 |
| [[MySQL/01-Foundations/StorageEngine-存储引擎|StorageEngine 存储引擎]] | InnoDB、事务、锁、索引与持久化能力 |

## 02-Schema-DDL 表结构

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/02-Schema-DDL/CREATE-DATABASE-创建数据库|CREATE DATABASE 创建数据库]] | 创建数据库与字符集 |
| [[MySQL/02-Schema-DDL/CREATE-TABLE-创建表|CREATE TABLE 创建表]] | 字段、主键、约束、引擎 |
| [[MySQL/02-Schema-DDL/ALTER-TABLE-修改表|ALTER TABLE 修改表]] | 表结构演进与上线风险 |
| [[MySQL/02-Schema-DDL/Online-DDL|Online DDL]] | 在线变更、锁级别和生产发布策略 |
| [[MySQL/02-Schema-DDL/PRIMARY-KEY-主键约束|PRIMARY KEY 主键约束]] | 主键约束与聚簇索引 |
| [[MySQL/02-Schema-DDL/UNIQUE-唯一约束|UNIQUE 唯一约束]] | 唯一性、幂等与去重 |
| [[MySQL/02-Schema-DDL/FOREIGN-KEY-外键约束|FOREIGN KEY 外键约束]] | 引用完整性与级联风险 |
| [[MySQL/02-Schema-DDL/NOT-NULL-非空约束|NOT NULL 非空约束]] | 必填字段与空值语义 |
| [[MySQL/02-Schema-DDL/DEFAULT-默认值|DEFAULT 默认值]] | 默认状态与插入行为 |
| [[MySQL/02-Schema-DDL/DROP-TABLE-删除表|DROP TABLE 删除表]] | 删除表结构与数据 |
| [[MySQL/02-Schema-DDL/DROP-DATABASE-删除数据库|DROP DATABASE 删除数据库]] | 删除整库的风险边界 |

## 03-SQL-DML 增删改查

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/03-SQL-DML/SELECT-基础查询|SELECT 基础查询]] | 字段选择与基础读取 |
| [[MySQL/03-SQL-DML/INSERT-单行插入|INSERT 单行插入]] | 单条业务新增 |
| [[MySQL/03-SQL-DML/INSERT-批量插入|INSERT 批量插入]] | 多行插入与批量写入 |
| [[MySQL/03-SQL-DML/INSERT-IGNORE|INSERT IGNORE]] | 冲突忽略与导入去重 |
| [[MySQL/03-SQL-DML/INSERT-ON-DUPLICATE-KEY-UPDATE|INSERT ON DUPLICATE KEY UPDATE]] | upsert 与唯一键冲突更新 |
| [[MySQL/03-SQL-DML/UPDATE-条件更新|UPDATE 条件更新]] | 单条或明确范围更新 |
| [[MySQL/03-SQL-DML/UPDATE-批量更新|UPDATE 批量更新]] | 批处理更新与锁风险 |
| [[MySQL/03-SQL-DML/DELETE-条件删除|DELETE 条件删除]] | 条件删除与安全边界 |
| [[MySQL/03-SQL-DML/TRUNCATE-与-DELETE-区别|TRUNCATE 与 DELETE 区别]] | 清空表与条件删除差异 |
| [[MySQL/03-SQL-DML/Soft-Delete-逻辑删除|Soft Delete 逻辑删除]] | 逻辑删除字段与查询约束 |

## 04-Query-Methods 查询方法

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/04-Query-Methods/WHERE-条件过滤|WHERE 条件过滤]] | 行级过滤与安全边界 |
| [[MySQL/04-Query-Methods/LIKE-模糊查询|LIKE 模糊查询]] | 前缀匹配、全模糊与索引影响 |
| [[MySQL/04-Query-Methods/IN-多值匹配|IN 多值匹配]] | 多值条件与批量查询 |
| [[MySQL/04-Query-Methods/BETWEEN-范围查询|BETWEEN 范围查询]] | 范围条件与时间边界 |
| [[MySQL/04-Query-Methods/IS-NULL-空值判断|IS NULL 空值判断]] | NULL 判断与索引语义 |
| [[MySQL/04-Query-Methods/ORDER-BY-排序|ORDER BY 排序]] | 结果排序与索引排序 |
| [[MySQL/04-Query-Methods/LIMIT-分页|LIMIT 分页]] | 浅分页、深分页与 seek pagination |
| [[MySQL/04-Query-Methods/COUNT-统计|COUNT 统计]] | 行数统计与计数成本 |
| [[MySQL/04-Query-Methods/Aggregate-聚合函数|Aggregate 聚合函数]] | SUM、AVG、MAX、MIN |
| [[MySQL/04-Query-Methods/GROUP-BY-分组|GROUP BY 分组]] | 分组汇总 |
| [[MySQL/04-Query-Methods/HAVING-分组后过滤|HAVING 分组后过滤]] | 聚合结果过滤 |
| [[MySQL/04-Query-Methods/INNER-JOIN|INNER JOIN]] | 内连接 |
| [[MySQL/04-Query-Methods/LEFT-JOIN|LEFT JOIN]] | 左连接 |
| [[MySQL/04-Query-Methods/RIGHT-JOIN|RIGHT JOIN]] | 右连接 |
| [[MySQL/04-Query-Methods/Subquery-子查询|Subquery 子查询]] | 子查询与改写思路 |

## 05-Indexing 索引

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/05-Indexing/Index-索引概念|Index 索引概念]] | 索引收益与写入成本 |
| [[MySQL/05-Indexing/B+Tree-索引结构|B+Tree 索引结构]] | InnoDB 索引的页、层级和范围扫描基础 |
| [[MySQL/05-Indexing/Primary-Index-主键索引|Primary Index 主键索引]] | 聚簇索引与主键设计 |
| [[MySQL/05-Indexing/Secondary-Index-二级索引|Secondary Index 二级索引]] | 二级索引、主键值与回表 |
| [[MySQL/05-Indexing/Unique-Index-唯一索引|Unique Index 唯一索引]] | 唯一性与高选择性查询 |
| [[MySQL/05-Indexing/Normal-Index-普通索引|Normal Index 普通索引]] | 普通字段查询加速 |
| [[MySQL/05-Indexing/Composite-Index-联合索引|Composite Index 联合索引]] | 多字段查询与排序 |
| [[MySQL/05-Indexing/Leftmost-Prefix-最左前缀原则|Leftmost Prefix 最左前缀原则]] | 联合索引匹配规则 |
| [[MySQL/05-Indexing/Covering-Index-覆盖索引|Covering Index 覆盖索引]] | 减少回表 |
| [[MySQL/05-Indexing/Index-Selectivity-索引选择性|Index Selectivity 索引选择性]] | 区分度、基数与索引价值 |
| [[MySQL/05-Indexing/Index-Condition-Pushdown-索引下推|Index Condition Pushdown 索引下推]] | 引擎层条件过滤 |
| [[MySQL/05-Indexing/Index-Failure-索引失效场景|Index Failure 索引失效场景]] | 函数、隐式转换、左模糊等 |
| [[MySQL/05-Indexing/SHOW-INDEX-查看索引|SHOW INDEX 查看索引]] | 索引盘点 |

## 06-Transaction-Lock 事务与锁

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/06-Transaction-Lock/Transaction-事务|Transaction 事务]] | 多 SQL 原子操作 |
| [[MySQL/06-Transaction-Lock/ACID|ACID]] | 事务四大性质 |
| [[MySQL/06-Transaction-Lock/Isolation-Level-隔离级别|Isolation Level 隔离级别]] | 四种隔离级别与并发现象 |
| [[MySQL/06-Transaction-Lock/MVCC|MVCC]] | 一致性读、Read View、undo 版本链 |
| [[MySQL/06-Transaction-Lock/Row-Lock-行锁|Row Lock 行锁]] | 索引记录锁 |
| [[MySQL/06-Transaction-Lock/Gap-Lock-间隙锁|Gap Lock 间隙锁]] | 范围间隙锁 |
| [[MySQL/06-Transaction-Lock/Next-Key-Lock|Next-Key Lock]] | 记录锁与间隙锁组合 |
| [[MySQL/06-Transaction-Lock/SELECT-FOR-UPDATE|SELECT FOR UPDATE]] | 事务内当前读加锁 |
| [[MySQL/06-Transaction-Lock/Deadlock-死锁|Deadlock 死锁]] | 死锁成因、排查和重试 |
| [[MySQL/06-Transaction-Lock/锁等待排查-Lock-Wait|锁等待排查 Lock Wait]] | 锁等待现场定位 |

## 07-InnoDB-Internals InnoDB 内核

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/07-InnoDB-Internals/InnoDB-架构总览|InnoDB 架构总览]] | 内存、磁盘、后台线程和日志协作 |
| [[MySQL/07-InnoDB-Internals/Buffer-Pool|Buffer Pool]] | 数据页缓存、LRU 和脏页 |
| [[MySQL/07-InnoDB-Internals/redo-log|redo log]] | 崩溃恢复和持久性 |
| [[MySQL/07-InnoDB-Internals/undo-log|undo log]] | 回滚和 MVCC 旧版本 |
| [[MySQL/07-InnoDB-Internals/binlog-与-redo-log-协作|binlog 与 redo log 协作]] | 两阶段提交和主从一致性基础 |
| [[MySQL/07-InnoDB-Internals/change-buffer|change buffer]] | 二级索引写入缓冲 |
| [[MySQL/07-InnoDB-Internals/doublewrite-buffer|doublewrite buffer]] | 防止页写入撕裂 |
| [[MySQL/07-InnoDB-Internals/checkpoint|checkpoint]] | 脏页刷盘和恢复边界 |
| [[MySQL/07-InnoDB-Internals/崩溃恢复-Crash-Recovery|崩溃恢复 Crash Recovery]] | redo、undo、binlog 的恢复路径 |

## 08-Performance-Diagnostics 性能排障

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/08-Performance-Diagnostics/EXPLAIN-使用方法|EXPLAIN 使用方法]] | 执行计划入口 |
| [[MySQL/08-Performance-Diagnostics/EXPLAIN-ANALYZE|EXPLAIN ANALYZE]] | 实际执行时间与行数验证 |
| [[MySQL/08-Performance-Diagnostics/optimizer-trace|optimizer trace]] | 优化器决策过程追踪 |
| [[MySQL/08-Performance-Diagnostics/Slow-Query-Log-慢查询日志|Slow Query Log 慢查询日志]] | 慢 SQL 发现入口 |
| [[MySQL/08-Performance-Diagnostics/慢-SQL-排查流程|慢 SQL 排查流程]] | 从现象到执行计划到修复验证 |
| [[MySQL/08-Performance-Diagnostics/JOIN-调优|JOIN 调优]] | 连接字段、驱动表和索引 |
| [[MySQL/08-Performance-Diagnostics/LIMIT-深分页调优|LIMIT 深分页调优]] | seek pagination 与覆盖索引 |
| [[MySQL/08-Performance-Diagnostics/Connection-Pool-连接池调优|Connection Pool 连接池调优]] | 连接池与数据库承载能力 |

## 09-Replication-HA 复制与高可用

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/09-Replication-HA/binlog-Binary-Log|binlog Binary Log]] | 数据变更复制与恢复日志 |
| [[MySQL/09-Replication-HA/relay-log|relay log]] | replica 本地中继日志 |
| [[MySQL/09-Replication-HA/GTID|GTID]] | 全局事务标识与自动定位 |
| [[MySQL/09-Replication-HA/主从复制-Replication|主从复制 Replication]] | source 到 replica 的复制链路 |
| [[MySQL/09-Replication-HA/半同步复制-Semisynchronous-Replication|半同步复制]] | 提交等待至少一个副本确认 |
| [[MySQL/09-Replication-HA/复制延迟-Replication-Lag|复制延迟]] | 延迟原因、观测与缓解 |
| [[MySQL/09-Replication-HA/故障切换-Failover|故障切换]] | 提升副本与切换流量 |
| [[MySQL/09-Replication-HA/读写分离-Read-Write-Splitting|读写分离]] | 读流量扩展与一致性风险 |

## 10-Operations 运维

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/10-Operations/备份恢复-Backup-Restore|备份恢复 Backup Restore]] | 逻辑备份、物理备份和恢复演练 |
| [[MySQL/10-Operations/权限与账号-Privilege|权限与账号 Privilege]] | 最小权限、账号分层与授权边界 |
| [[MySQL/10-Operations/安全配置-Security-Baseline|安全配置 Security Baseline]] | 网络、账号、日志和参数基线 |
| [[MySQL/10-Operations/参数配置-Server-Variables|参数配置 Server Variables]] | 参数变更、持久化和回滚 |
| [[MySQL/10-Operations/监控指标-Monitoring-Metrics|监控指标 Monitoring Metrics]] | 连接、QPS、锁、缓存、复制 |
| [[MySQL/10-Operations/容量评估-Capacity-Planning|容量评估 Capacity Planning]] | 数据量、索引、增长和保留周期 |
| [[MySQL/10-Operations/数据迁移-Data-Migration|数据迁移 Data Migration]] | 灰度、校验和回滚 |

## 11-Scaling-Architecture 架构扩展

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/11-Scaling-Architecture/分库分表-Sharding|分库分表 Sharding]] | 水平拆分、路由和聚合查询代价 |
| [[MySQL/11-Scaling-Architecture/Partition-分区表|Partition 分区表]] | 单表内部按范围或列表分区 |
| [[MySQL/11-Scaling-Architecture/冷热数据-Hot-Cold-Data|冷热数据]] | 保留策略、归档和查询路径 |
| [[MySQL/11-Scaling-Architecture/缓存一致性-Cache-Consistency|缓存一致性]] | cache aside、失效和双写风险 |
| [[MySQL/11-Scaling-Architecture/数据库边界与业务幂等|数据库边界与业务幂等]] | 唯一键、事务、消息和补偿 |

## 12-Java-Persistence Java 持久层

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/12-Java-Persistence/JDBC-URL|JDBC URL]] | Java 连接 MySQL 的 URL 结构和参数 |
| [[MySQL/12-Java-Persistence/DataSource-数据源|DataSource 数据源]] | Spring Boot 数据源配置和职责边界 |
| [[MySQL/12-Java-Persistence/MyBatis-数据访问|MyBatis 数据访问]] | Mapper、注解 SQL、参数绑定和字段映射 |
| [[MySQL/12-Java-Persistence/MyBatis-select-方法|MyBatis select 方法]] | 查询方法 |
| [[MySQL/12-Java-Persistence/MyBatis-insert-方法|MyBatis insert 方法]] | 新增方法 |
| [[MySQL/12-Java-Persistence/MyBatis-update-方法|MyBatis update 方法]] | 更新方法 |
| [[MySQL/12-Java-Persistence/MyBatis-delete-方法|MyBatis delete 方法]] | 删除方法 |
| [[MySQL/12-Java-Persistence/MyBatis-参数绑定|MyBatis 参数绑定]] | #{}、${} 与 @Param |
| [[MySQL/12-Java-Persistence/MyBatis-动态-SQL|MyBatis 动态 SQL]] | XML 动态条件 |
| [[MySQL/12-Java-Persistence/MyBatis-批量操作|MyBatis 批量操作]] | 批量写入、foreach 和事务边界 |

## 99-Common-Concepts 通用概念

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/99-Common-Concepts/当前读与快照读|当前读与快照读]] | MVCC、锁和 SELECT 语义的分界 |
| [[MySQL/99-Common-Concepts/回表|回表]] | 二级索引定位主键再访问聚簇索引 |
| [[MySQL/99-Common-Concepts/选择性与基数|选择性与基数]] | 索引价值评估的通用指标 |
| [[MySQL/99-Common-Concepts/幂等写入|幂等写入]] | 唯一键、upsert 与业务重试 |

## 参考资料

- [MySQL 8.4 Reference Manual](https://dev.mysql.com/doc/refman/8.4/en/)
- [MySQL Releases: Innovation and LTS](https://dev.mysql.com/doc/refman/8.4/en/mysql-releases.html)
