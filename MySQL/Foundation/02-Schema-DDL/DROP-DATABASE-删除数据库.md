---
title: DROP DATABASE 删除数据库
description: 面向初学者讲解 DROP DATABASE 的行为,风险,执行前检查和恢复边界.
tags:
  - MySQL
  - DDL
category: MySQL
mysql-version: "8.4 LTS"
---

# DROP DATABASE 删除数据库

`DROP DATABASE` 删除一个数据库,以及其中由 MySQL 管理的表和其他对象.这是整章风险最高的操作之一:语句成功后不能用普通 `ROLLBACK` 撤销.

> [!danger] 先确认,再执行
> 不要仅凭终端提示符判断环境.执行前显式查询服务器,当前库和目标库中的对象,并确认备份可恢复.

## 1. 基本语法

```sql
DROP DATABASE learning_db;
```

目标不存在时避免脚本报错:

```sql
DROP DATABASE IF EXISTS learning_db;
```

`IF EXISTS` 只处理目标不存在的情况,不会降低误删一个真实数据库的风险.

## 2. 删除前的最小核对流程

```sql

-- 确认当前服务器和账号
SELECT @@hostname, @@port, CURRENT_USER();

-- 确认当前默认数据库
SELECT DATABASE();

-- 确认目标存在及其默认字符集
SHOW CREATE DATABASE learning_db;

-- 查看目标库内的基础表和视图
SELECT TABLE_NAME, TABLE_TYPE
FROM information_schema.TABLES
WHERE TABLE_SCHEMA = 'learning_db'
ORDER BY TABLE_TYPE, TABLE_NAME;
```

然后在组织规定的备份系统中确认备份时间,保留周期和恢复演练结果.只有"有备份文件"而没有验证恢复流程,并不等于可以恢复.

## 3. 删除后会发生什么

- 目标数据库的表和其中数据被删除.
- 视图,触发器,存储程序和事件等库内对象也可能被删除.
- 当前连接如果正在使用该库,后续不带库名前缀的语句将无法正常使用它.
- 应用配置,报表,定时任务和监控不会自动更新,它们可能持续报错.
- 语句通常会隐式提交,不能依赖事务回滚.

不要用文件管理器直接删除 MySQL 数据目录.数据字典,表空间和服务器管理的文件必须由 MySQL 语句和受控运维流程处理.

## 4. 什么场景可以使用

适合:

- 删除可随时重建的本地练习库.
- 清理生命周期已结束,已经完成归档的临时测试环境.
- 按正式下线流程移除废弃数据库.

不适合:

- 只想删除几行数据,应使用带条件的 `DELETE`.
- 只想清空一张表,应先比较 `DELETE` 与 `TRUNCATE`.
- 只想删除一张废弃表,应使用 [DROP TABLE 删除表](DROP-TABLE-删除表.md).
- 试图通过删库解决性能或磁盘治理问题.

## 5. 生产环境下线顺序

1. 找出应用,账号,报表,作业,复制和监控依赖.
2. 停止写入,让依赖方迁移到新库或新服务.
3. 观察一段约定的静默期,确认没有真实访问.
4. 完成一致性备份,并验证能够恢复到独立环境.
5. 回收或禁止应用账号访问,降低误写回流风险.
6. 在变更窗口再次确认实例与库名,执行删除.
7. 验证对象消失,并清理配置,权限与监控.

对高价值数据库,常见做法是先逻辑下线并保留一段时间,而不是确认"没人说在用"后立刻删除.

## 6. 常见错误

| 错误 | 后果 | 预防 |
| :--- | :--- | :--- |
| 连错生产实例 | 删除真实业务库 | 查询 `@@hostname`,端口和账号,使用环境隔离 |
| 库名变量为空或错误 | 脚本指向意外目标 | 禁止拼接未经校验的动态 DDL |
| 把 `IF EXISTS` 当安全开关 | 真实目标仍会被删除 | 人工与自动检查目标身份 |
| 只备份未演练恢复 | 事故后才发现备份不可用 | 定期恢复演练并记录 RPO/RTO |
| 忽略外部依赖 | 删除后应用持续故障 | 先做依赖清单和静默期观察 |

## 7. 删除后的验证

```sql

SELECT SCHEMA_NAME
FROM information_schema.SCHEMATA
WHERE SCHEMA_NAME = 'learning_db';
```

结果为空表示数据字典中已不存在该数据库.还应验证应用错误率,任务状态和监控告警,而不只看 SQL 是否返回成功.

## 8. 相关主题

- [DDL 数据定义语言百科](DDL-数据定义语言百科.md)
- [CREATE DATABASE 创建数据库](CREATE-DATABASE-创建数据库.md)
- [DROP TABLE 删除表](DROP-TABLE-删除表.md)
- [TRUNCATE 与 DELETE 区别](TRUNCATE-与-DELETE-区别.md)
- [数据备份 Backup](../10-Operations/备份恢复-Backup-Restore.md)

## 9. 官方参考

- [MySQL 8.4 Reference Manual: DROP DATABASE Statement](https://dev.mysql.com/doc/refman/8.4/en/drop-database.html)
