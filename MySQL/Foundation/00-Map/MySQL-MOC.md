---
title: MySQL MOC
description: MySQL 系统知识索引,按基础,SQL,索引,事务锁,InnoDB,性能排障,复制高可用,运维,架构扩展与 Java 持久层分层组织.
tags:
  - MySQL
  - SQL
  - MOC
category: MySQL
---

# MySQL MOC

> 主线版本以 MySQL 8.4 LTS 为准.学习目标是同时服务复习,面试追问和 Spring Boot / MyBatis 项目落地.目录名全部使用英文和 hyphen,文件名保留中英文混合,方便检索.

## 学习路线
1. 先读 [基础概念](Database-数据库概念.md),[表](Table-表.md),[字段](Column-字段.md) 和 [数据类型](DataType-数据类型.md),建立 schema,列语义,字符集和完整类型选型意识.
2. 再读 [DDL](CREATE-TABLE-创建表.md),[DML](SELECT-基础查询.md),[DQL](DQL-MOC.md) 和 [DCL](DCL-MOC.md),把 SQL 写法与权限控制练熟.
3. 接着读 [B+Tree](B+Tree-索引结构.md),[联合索引](Composite-Index-联合索引.md),[MVCC](MVCC.md),[隔离级别](Isolation-Level-隔离级别.md),理解性能和并发.
4. 然后读 [Buffer Pool](Buffer-Pool.md),[redo log](redo-log.md),[binlog 与 redo log 协作](binlog-与-redo-log-协作.md),串起 InnoDB 的可靠性机制.
5. 最后读 [慢 SQL 排查流程](慢-SQL-排查流程.md),[主从复制](主从复制-Replication.md),[备份恢复](备份恢复-Backup-Restore.md),[MyBatis 批量操作](MyBatis-批量操作.md),落到线上诊断与项目实践.

## 0-BASES 字段类型深入

> 以下专题以 MySQL 8.4 LTS 为版本边界.先用 [DataType 数据类型](DataType-数据类型.md) 建立完整选型框架,再按数据域深入.

| 笔记 | 覆盖类型与作用 |
| :--- | :--- |
| [整数类型](整数类型.md) | `TINYINT`,`SMALLINT`,`MEDIUMINT`,`INT`,`BIGINT`,`UNSIGNED`,溢出与语言映射 |
| [浮点类型](浮点类型.md) | `FLOAT`,`DOUBLE`,`REAL`,有效数字,误差和容差比较 |
| [定点与位类型](定点与位类型.md) | `DECIMAL` / `NUMERIC`,`BIT`,`BOOL` / `BOOLEAN`,精确值和位标志 |
| [字符串与二进制类型](字符串.md) | `CHAR`,`VARCHAR`,TEXT/BLOB 四级类型,`BINARY`,`VARBINARY`,字符集和字节语义 |
| [枚举与集合类型](枚举与集合类型.md) | `ENUM`,`SET`,内部序号,DDL 演进和规范化替代方案 |
| [日期时间类型](日期.md) | `DATE`,`TIME`,`DATETIME`,`TIMESTAMP`,`YEAR`,小数秒与时区 |
| [JSON 类型](JSON-类型.md) | 二进制 JSON,路径,`JSON_TABLE`,部分更新,generated/函数/多值索引 |
| [空间数据类型](空间数据类型.md) | `GEOMETRY` 与 7 个具体子类型,SRID,空间函数和 `SPATIAL INDEX` |

## 01-Foundations 基础

| 笔记 | 说明 |
| :--- | :--- |
| [Database 数据库概念](Database-数据库概念.md) | schema,业务域与权限边界 |
| [Table 表](Table-表.md) | 表,行,列,约束与索引的容器 |
| [Column 字段](Column-字段.md) | 字段语义,类型,默认值与可空性 |
| [DataType 数据类型](DataType-数据类型.md) | 数值,字符串,时间,金额类型选择 |
| [Charset 字符集与排序规则](Charset-字符集与排序规则.md) | utf8mb4,collation,比较与排序 |
| [StorageEngine 存储引擎](StorageEngine-存储引擎.md) | InnoDB,事务,锁,索引与持久化能力 |

## 02-Schema-DDL 表结构

| 笔记 | 说明 |
| :--- | :--- |
| [CREATE DATABASE 创建数据库](CREATE-DATABASE-创建数据库.md) | 创建数据库与字符集 |
| [CREATE TABLE 创建表](CREATE-TABLE-创建表.md) | 字段,主键,约束,引擎 |
| [数据约束 Constraint 百科](数据约束-Constraint-百科.md) | 面向新手的主键,非空,默认值,唯一,外键与 CHECK 语句总览 |
| [ALTER TABLE 修改表](ALTER-TABLE-修改表.md) | 表结构演进与上线风险 |
| [Online DDL](Online-DDL.md) | 在线变更,锁级别和生产发布策略 |
| [PRIMARY KEY 主键约束](PRIMARY-KEY-主键约束.md) | 主键约束与聚簇索引 |
| [UNIQUE 唯一约束](UNIQUE-唯一约束.md) | 唯一性,幂等与去重 |
| [FOREIGN KEY 外键约束](FOREIGN-KEY-外键约束.md) | 引用完整性与级联风险 |
| [NOT NULL 非空约束](NOT-NULL-非空约束.md) | 必填字段与空值语义 |
| [DEFAULT 默认值](DEFAULT-默认值.md) | 默认状态与插入行为 |
| [DROP TABLE 删除表](DROP-TABLE-删除表.md) | 删除表结构与数据 |
| [DROP DATABASE 删除数据库](DROP-DATABASE-删除数据库.md) | 删除整库的风险边界 |

## 03-SQL-DML 增删改查

| 笔记 | 说明 |
| :--- | :--- |
| [SELECT 基础查询](SELECT-基础查询.md) | 字段选择与基础读取 |
| [INSERT 单行插入](INSERT-单行插入.md) | 单条业务新增 |
| [INSERT 批量插入](INSERT-批量插入.md) | 多行插入与批量写入 |
| [INSERT IGNORE](INSERT-IGNORE.md) | 冲突忽略与导入去重 |
| [INSERT ON DUPLICATE KEY UPDATE](INSERT-ON-DUPLICATE-KEY-UPDATE.md) | upsert 与唯一键冲突更新 |
| [UPDATE 条件更新](UPDATE-条件更新.md) | 单条或明确范围更新 |
| [UPDATE 批量更新](UPDATE-批量更新.md) | 批处理更新与锁风险 |
| [DELETE 条件删除](DELETE-条件删除.md) | 条件删除与安全边界 |
| [TRUNCATE 与 DELETE 区别](TRUNCATE-与-DELETE-区别.md) | 清空表与条件删除差异 |
| [Soft Delete 逻辑删除](Soft-Delete-逻辑删除.md) | 逻辑删除字段与查询约束 |

## 03.5-SQL-DCL 数据控制语言

> 从 [DCL 数据控制语言](DCL-MOC.md) 开始,学习账号,授权,撤权,角色和最小权限原则.

| 笔记 | 说明 |
| :--- | :--- |
| [DCL 数据控制语言](DCL-MOC.md) | `CREATE USER`,`GRANT`,`REVOKE`,`SHOW GRANTS` 与角色管理 |

## 04-Query-Methods 查询方法
> 初学者建议先从 [DQL MOC](DQL-MOC.md) 开始;该入口按单表过滤,聚合,多表连接和高级分析分层,并包含执行顺序,练习与性能自检.

| 笔记 | 说明 |
| :--- | :--- |
| [WHERE 条件过滤](WHERE-条件过滤.md) | 行级过滤与安全边界 |
| [LIKE 模糊查询](LIKE-模糊查询.md) | 前缀匹配,全模糊与索引影响 |
| [IN 多值匹配](IN-多值匹配.md) | 多值条件与批量查询 |
| [BETWEEN 范围查询](BETWEEN-范围查询.md) | 范围条件与时间边界 |
| [IS NULL 空值判断](IS-NULL-空值判断.md) | NULL 判断与索引语义 |
| [ORDER BY 排序](ORDER-BY-排序.md) | 结果排序与索引排序 |
| [LIMIT 分页](LIMIT-分页.md) | 浅分页,深分页与 seek pagination |
| [COUNT 统计](COUNT-统计.md) | 行数统计与计数成本 |
| [Aggregate 聚合函数](Aggregate-聚合函数.md) | SUM,AVG,MAX,MIN |
| [GROUP BY 分组](GROUP-BY-分组.md) | 分组汇总 |
| [HAVING 分组后过滤](HAVING-分组后过滤.md) | 聚合结果过滤 |
| [INNER JOIN](INNER-JOIN.md) | 内连接 |
| [LEFT JOIN](LEFT-JOIN.md) | 左连接 |
| [RIGHT JOIN](RIGHT-JOIN.md) | 右连接 |
| [Subquery 子查询](Subquery-子查询.md) | 子查询与改写思路 |

## 04.5-Functions 函数百科

> 从 [MySQL 函数百科](函数-MOC.md) 开始,按用途学习和查阅标量函数,聚合函数与窗口函数;每篇都包含语法,示例,NULL 行为,易错点和性能提示.

| 笔记 | 说明 |
| :--- | :--- |
| [MySQL 函数百科](函数-MOC.md) | 函数分类,学习路线与通用检查清单 |
| [字符串函数](字符串函数.md) | 拼接,截取,替换,清洗和字符长度 |
| [数值函数](数值函数.md) | 舍入,取整,绝对值,余数和精度 |
| [日期时间函数](日期时间函数.md) | 当前时间,日期加减,差值和格式化 |
| [NULL 与流程控制函数](NULL-与-流程控制函数.md) | 空值兜底,除零保护和条件分支 |
| [类型转换与系统信息函数](类型转换与系统信息函数.md) | 显式转换,版本,用户和连接信息 |
| [JSON 函数](JSON函数.md) | JSON 路径,读取,修改,判断和索引 |
| [Aggregate 聚合函数](Aggregate-聚合函数.md) | COUNT,SUM,AVG,MAX 和 MIN |
| [Window Functions 窗口函数](Window-Functions-窗口函数.md) | 排名,累计,前后行和组内分析 |
## 05-Indexing 索引

| 笔记 | 说明 |
| :--- | :--- |
| [Index 索引概念](Index-索引概念.md) | 索引收益与写入成本 |
| [B+Tree 索引结构](B+Tree-索引结构.md) | InnoDB 索引的页,层级和范围扫描基础 |
| [Primary Index 主键索引](Primary-Index-主键索引.md) | 聚簇索引与主键设计 |
| [Secondary Index 二级索引](Secondary-Index-二级索引.md) | 二级索引,主键值与回表 |
| [Unique Index 唯一索引](Unique-Index-唯一索引.md) | 唯一性与高选择性查询 |
| [Normal Index 普通索引](Normal-Index-普通索引.md) | 普通字段查询加速 |
| [Composite Index 联合索引](Composite-Index-联合索引.md) | 多字段查询与排序 |
| [Leftmost Prefix 最左前缀原则](Leftmost-Prefix-最左前缀原则.md) | 联合索引匹配规则 |
| [Covering Index 覆盖索引](Covering-Index-覆盖索引.md) | 减少回表 |
| [Index Selectivity 索引选择性](Index-Selectivity-索引选择性.md) | 区分度,基数与索引价值 |
| [Index Condition Pushdown 索引下推](Index-Condition-Pushdown-索引下推.md) | 引擎层条件过滤 |
| [Index Failure 索引失效场景](Index-Failure-索引失效场景.md) | 函数,隐式转换,左模糊等 |
| [SHOW INDEX 查看索引](SHOW-INDEX-查看索引.md) | 索引盘点 |

## 06-Transaction-Lock 事务与锁

| 笔记 | 说明 |
| :--- | :--- |
| [Transaction 事务](Transaction-事务.md) | 多 SQL 原子操作 |
| [ACID](ACID.md) | 事务四大性质 |
| [Isolation Level 隔离级别](Isolation-Level-隔离级别.md) | 四种隔离级别与并发现象 |
| [MVCC](MVCC.md) | 一致性读,Read View,undo 版本链 |
| [Row Lock 行锁](Row-Lock-行锁.md) | 索引记录锁 |
| [Gap Lock 间隙锁](Gap-Lock-间隙锁.md) | 范围间隙锁 |
| [Next-Key Lock](Next-Key-Lock.md) | 记录锁与间隙锁组合 |
| [SELECT FOR UPDATE](SELECT-FOR-UPDATE.md) | 事务内当前读加锁 |
| [Deadlock 死锁](Deadlock-死锁.md) | 死锁成因,排查和重试 |
| [锁等待排查 Lock Wait](锁等待排查-Lock-Wait.md) | 锁等待现场定位 |

## 07-InnoDB-Internals InnoDB 内核

| 笔记 | 说明 |
| :--- | :--- |
| [InnoDB 架构总览](InnoDB-架构总览.md) | 内存,磁盘,后台线程和日志协作 |
| [Buffer Pool](Buffer-Pool.md) | 数据页缓存,LRU 和脏页 |
| [redo log](redo-log.md) | 崩溃恢复和持久性 |
| [undo log](undo-log.md) | 回滚和 MVCC 旧版本 |
| [binlog 与 redo log 协作](binlog-与-redo-log-协作.md) | 两阶段提交和主从一致性基础 |
| [change buffer](change-buffer.md) | 二级索引写入缓冲 |
| [doublewrite buffer](doublewrite-buffer.md) | 防止页写入撕裂 |
| [checkpoint](checkpoint.md) | 脏页刷盘和恢复边界 |
| [崩溃恢复 Crash Recovery](崩溃恢复-Crash-Recovery.md) | redo,undo,binlog 的恢复路径 |

## 08-Performance-Diagnostics 性能排障
| 笔记 | 说明 |
| :--- | :--- |
| [EXPLAIN 使用方法](EXPLAIN-使用方法.md) | 12 个核心输出字段,可执行实验和阅读顺序 |
| [EXPLAIN id 字段](EXPLAIN-id-字段.md) | 查询块编号和执行层次 |
| [EXPLAIN select_type 字段](EXPLAIN-select_type-字段.md) | SIMPLE,PRIMARY,SUBQUERY,DERIVED 等查询类型 |
| [EXPLAIN type 字段](EXPLAIN-type-字段.md) | system,const,eq_ref,ref,range,index,ALL 等访问方式 |
| [EXPLAIN key 字段](EXPLAIN-key-字段.md) | 优化器实际选择的索引 |
| [EXPLAIN rows 字段](EXPLAIN-rows-字段.md) | 预计扫描行数及估算误差 |
| [EXPLAIN Extra 字段](EXPLAIN-Extra-字段.md) | Using index,Using filesort,Using temporary 等附加信息 |
| [EXPLAIN ANALYZE](EXPLAIN-ANALYZE.md) | 实际执行时间与行数验证 |
| [optimizer trace](optimizer-trace.md) | 优化器决策过程追踪 |
| [Slow Query Log 慢查询日志](Slow-Query-Log-慢查询日志.md) | 慢 SQL 发现入口 |
| [慢 SQL 排查流程](慢-SQL-排查流程.md) | 从现象到执行计划到修复验证 |
| [SELECT 调优](SELECT-调优.md) | 列裁剪,访问路径和回表成本 |
| [WHERE 调优](WHERE-调优.md) | 谓词可索引性,隐式转换与选择性 |
| [JOIN 调优](JOIN-调优.md) | 连接字段,驱动表和索引 |
| [GROUP BY 调优](GROUP-BY-调优.md) | 分组索引,临时表和聚合成本 |
| [ORDER BY 调优](ORDER-BY-调优.md) | 索引顺序,filesort 和 LIMIT |
| [LIMIT 深分页调优](LIMIT-深分页调优.md) | seek pagination 与覆盖索引 |
| [Batch Insert 批量写入调优](Batch-Insert-批量写入调优.md) | 批次大小,事务,网络与日志权衡 |
| [Connection Pool 连接池调优](Connection-Pool-连接池调优.md) | 连接池与数据库承载能力 |

## 09-Replication-HA 复制与高可用

| 笔记 | 说明 |
| :--- | :--- |
| [binlog Binary Log](binlog-Binary-Log.md) | 数据变更复制与恢复日志 |
| [relay log](relay-log.md) | replica 本地中继日志 |
| [GTID](GTID.md) | 全局事务标识与自动定位 |
| [主从复制 Replication](主从复制-Replication.md) | source 到 replica 的复制链路 |
| [半同步复制](半同步复制-Semisynchronous-Replication.md) | 提交等待至少一个副本确认 |
| [复制延迟](复制延迟-Replication-Lag.md) | 延迟原因,观测与缓解 |
| [故障切换](故障切换-Failover.md) | 提升副本与切换流量 |
| [读写分离](读写分离-Read-Write-Splitting.md) | 读流量扩展与一致性风险 |

## 10-Operations 运维

| 笔记 | 说明 |
| :--- | :--- |
| [备份恢复 Backup Restore](备份恢复-Backup-Restore.md) | 逻辑备份,物理备份和恢复演练 |
| [DCL 数据控制语言](DCL-MOC.md) | 最小权限,账号分层与授权边界 |
| [安全配置 Security Baseline](安全配置-Security-Baseline.md) | 网络,账号,日志和参数基线 |
| [参数配置 Server Variables](参数配置-Server-Variables.md) | 参数变更,持久化和回滚 |
| [监控指标 Monitoring Metrics](监控指标-Monitoring-Metrics.md) | 连接,QPS,锁,缓存,复制 |
| [容量评估 Capacity Planning](容量评估-Capacity-Planning.md) | 数据量,索引,增长和保留周期 |
| [数据迁移 Data Migration](数据迁移-Data-Migration.md) | 灰度,校验和回滚 |

## 11-Scaling-Architecture 架构扩展

| 笔记 | 说明 |
| :--- | :--- |
| [分库分表 Sharding](分库分表-Sharding.md) | 水平拆分,路由和聚合查询代价 |
| [Partition 分区表](Partition-分区表.md) | 单表内部按范围或列表分区 |
| [冷热数据](冷热数据-Hot-Cold-Data.md) | 保留策略,归档和查询路径 |
| [缓存一致性](缓存一致性-Cache-Consistency.md) | cache aside,失效和双写风险 |
| [数据库边界与业务幂等](数据库边界与业务幂等.md) | 唯一键,事务,消息和补偿 |

## 12-Java-Persistence Java 持久层

| 笔记 | 说明 |
| :--- | :--- |
| [JDBC URL](JDBC-URL.md) | Java 连接 MySQL 的 URL 结构和参数 |
| [DataSource 数据源](DataSource-数据源.md) | Spring Boot 数据源配置和职责边界 |
| [MyBatis 数据访问](MyBatis-数据访问.md) | Mapper,注解 SQL,参数绑定和字段映射 |
| [MyBatis select 方法](MyBatis-select-方法.md) | 查询方法 |
| [MyBatis insert 方法](MyBatis-insert-方法.md) | 新增方法 |
| [MyBatis update 方法](MyBatis-update-方法.md) | 更新方法 |
| [MyBatis delete 方法](MyBatis-delete-方法.md) | 删除方法 |
| [MyBatis 参数绑定](MyBatis-参数绑定.md) | #{},${} 与 @Param |
| [MyBatis 动态 SQL](MyBatis-动态-SQL.md) | XML 动态条件 |
| [MyBatis 批量操作](MyBatis-批量操作.md) | 批量写入,foreach 和事务边界 |

## 99-Common-Concepts 通用概念

| 笔记 | 说明 |
| :--- | :--- |
| [当前读与快照读](当前读与快照读.md) | MVCC,锁和 SELECT 语义的分界 |
| [回表](回表.md) | 二级索引定位主键再访问聚簇索引 |
| [选择性与基数](选择性与基数.md) | 索引价值评估的通用指标 |
| [幂等写入](幂等写入.md) | 唯一键,upsert 与业务重试 |

## 参考资料

- [MySQL 8.4 Reference Manual](https://dev.mysql.com/doc/refman/8.4/en/)
- [MySQL Releases: Innovation and LTS](https://dev.mysql.com/doc/refman/8.4/en/mysql-releases.html)
