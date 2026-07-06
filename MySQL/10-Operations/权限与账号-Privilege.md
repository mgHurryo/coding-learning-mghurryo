---
title: 权限与账号 Privilege
description: 说明 MySQL 账号、权限、最小权限原则和应用账号设计。
tags:
  - MySQL
  - Operations
  - Security
category: MySQL
---

# 权限与账号 Privilege

## 速览

MySQL 权限管理的目标是让每个账号只拥有完成职责所需的最小权限。应用账号、迁移账号、只读账号和运维账号应分离。

## 核心机制

MySQL 账号由 user 和 host 共同标识。授权可以到全局、库、表、列、例程等层级。生产应用通常只需要指定库的 DML 权限，不应使用 root 或超大权限账号。

## SQL/配置示例

```sql
CREATE USER 'app_user'@'%' IDENTIFIED BY 'strong-password';
GRANT SELECT, INSERT, UPDATE, DELETE ON app_db.* TO 'app_user'@'%';
SHOW GRANTS FOR 'app_user'@'%';
```

## 项目落地

Spring Boot 配置中使用应用专用账号；DDL 迁移使用独立发布账号；报表使用只读账号。密码放在密钥管理系统，不写进仓库。

## 常见错误

不要给应用账号 `SUPER`、`DROP`、`GRANT OPTION` 等高危权限；不要多个系统共用一个数据库账号。

## 相关主题

- [[MySQL/10-Operations/安全配置-Security-Baseline|安全配置 Security Baseline]]
- [[MySQL/12-Java-Persistence/DataSource-数据源|DataSource 数据源]]
