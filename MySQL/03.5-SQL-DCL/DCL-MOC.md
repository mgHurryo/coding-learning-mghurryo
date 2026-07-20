---
title: DCL 数据控制语言
description: 说明 MySQL 账号、授权、撤权、角色、权限检查与最小权限原则。
tags:
  - MySQL
  - SQL
  - DCL
  - Security
category: MySQL
---

# DCL 数据控制语言

## 速览

DCL（Data Control Language，数据控制语言）用于控制谁能连接 MySQL，以及账号能够访问哪些数据库对象、执行哪些操作。MySQL 中常见的 DCL 相关语句包括 `GRANT`、`REVOKE` 和 `SHOW GRANTS`；账号与角色管理通常也与 DCL 一起学习。

核心目标是落实最小权限原则：每个账号只拥有完成职责所需的权限，并且应用账号、迁移账号、只读账号和运维账号相互分离。

## 核心语句

| 语句 | 作用 |
| :--- | :--- |
| `CREATE USER` | 创建账号并设置认证信息 |
| `ALTER USER` | 修改认证方式、密码或账号状态 |
| `DROP USER` | 删除账号及其授权关系 |
| `GRANT` | 向账号或角色授予权限 |
| `REVOKE` | 撤销账号或角色的权限 |
| `SHOW GRANTS` | 查看账号或角色当前拥有的权限 |
| `CREATE ROLE` | 创建可复用的权限集合 |
| `SET DEFAULT ROLE` | 设置账号登录后默认启用的角色 |

## 账号与权限范围

MySQL 账号由 `user` 和 `host` 共同标识，例如 `'app_user'@'10.%'`。`'app_user'@'localhost'` 与 `'app_user'@'%'` 是两个不同账号；生产环境应尽量限制来源主机，避免无必要地使用 `%`。

授权范围可以是全局、数据库、表、列或存储程序。范围越大，误操作和数据泄露的影响面越大。常见写法如下：

| 授权目标 | 示例 | 范围 |
| :--- | :--- | :--- |
| 全局 | `*.*` | 所有数据库对象 |
| 数据库 | `app_db.*` | 指定数据库中的全部对象 |
| 表 | `app_db.orders` | 指定表 |
| 列 | `SELECT (id, status)` | 指定表中的部分列 |

## 基础授权流程

```sql
CREATE USER 'app_user'@'10.%'
    IDENTIFIED BY 'strong-password';

GRANT SELECT, INSERT, UPDATE, DELETE
    ON app_db.*
    TO 'app_user'@'10.%';

SHOW GRANTS FOR 'app_user'@'10.%';
```

MySQL 8.4 中，`GRANT` 不负责隐式创建账号，应先使用 `CREATE USER`。账号和权限变更会立即生效，通常不需要手动执行 `FLUSH PRIVILEGES`；只有直接修改授权表等特殊场景才需要重新加载权限。

## 撤销权限与删除账号

```sql
REVOKE INSERT, UPDATE, DELETE
    ON app_db.*
    FROM 'app_user'@'10.%';

SHOW GRANTS FOR 'app_user'@'10.%';

DROP USER 'app_user'@'10.%';
```

`REVOKE` 只撤销指定权限，不会删除账号。`DROP USER` 才会删除账号；执行前应确认应用连接、定时任务和运维脚本已经停用或迁移。

## 使用角色复用权限

```sql
CREATE ROLE 'app_readonly';

GRANT SELECT
    ON app_db.*
    TO 'app_readonly';

GRANT 'app_readonly'
    TO 'report_user'@'10.%';

SET DEFAULT ROLE 'app_readonly'
    TO 'report_user'@'10.%';
```

角色适合复用稳定的权限集合。把角色授予账号后，还应通过 `SET DEFAULT ROLE` 明确默认启用状态，并使用 `SHOW GRANTS` 验证最终结果。

## 项目落地

- Spring Boot 使用应用专用账号，只授予业务库必需的 DML 权限。
- DDL 迁移使用独立发布账号，不让常驻应用账号拥有建表、删表权限。
- 报表和数据分析账号优先使用只读角色，并限制可访问的库表。
- 密码放在密钥管理系统或受控环境变量中，不写入仓库。
- 权限变更纳入审批、审计和定期复核，及时回收离职人员与废弃服务账号。

## 常见错误

- 使用 `root` 运行应用，或给应用账号授予 `ALL PRIVILEGES`。
- 无必要地授予 `GRANT OPTION`，导致账号可以继续向其他账号授权。
- 多个系统共用同一个数据库账号，无法准确审计和独立回收权限。
- 只执行 `GRANT`，没有用 `SHOW GRANTS` 核对实际授权范围。
- 撤销部分权限后误以为账号已经删除，忽略其剩余权限和登录能力。

## 相关主题

- [安全配置 Security Baseline](../10-Operations/安全配置-Security-Baseline.md)
- [DataSource 数据源](../12-Java-Persistence/DataSource-数据源.md)
- [CREATE DATABASE 创建数据库](../02-Schema-DDL/CREATE-DATABASE-创建数据库.md)

## 参考资料

- [MySQL 8.4 Reference Manual: Access Control and Account Management](https://dev.mysql.com/doc/refman/8.4/en/access-control.html)
- [MySQL 8.4 Reference Manual: GRANT Statement](https://dev.mysql.com/doc/refman/8.4/en/grant.html)
- [MySQL 8.4 Reference Manual: REVOKE Statement](https://dev.mysql.com/doc/refman/8.4/en/revoke.html)
- [MySQL 8.4 Reference Manual: Using Roles](https://dev.mysql.com/doc/refman/8.4/en/roles.html)
