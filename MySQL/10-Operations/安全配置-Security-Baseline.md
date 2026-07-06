---
title: 安全配置 Security Baseline
description: 说明 MySQL 生产安全基线，包括网络暴露、账号、密码、权限、审计和备份保护。
tags:
  - MySQL
  - Operations
  - Security
category: MySQL
---

# 安全配置 Security Baseline

## 速览

MySQL 安全基线的目标是减少未授权访问、误操作和数据泄露风险。重点是网络边界、最小权限、强密码、加密传输、日志审计和备份保护。

## 核心机制

数据库不应直接暴露到公网；账号按职责拆分；应用账号只给业务库必要 DML；管理账号启用强认证和受控来源；敏感环境使用 TLS；备份文件也要加密和访问控制。

## SQL/配置示例

```sql
SHOW VARIABLES LIKE 'require_secure_transport';
SHOW GRANTS FOR 'app_user'@'%';
```

## 项目落地

Spring Boot 配置不能提交明文密码；生产连接信息应来自密钥管理或环境变量。测试库脱敏后再开放给开发和分析。

## 常见错误

不要把 root 账号给应用；不要多个服务共用账号；不要忽略备份文件泄露风险。

## 相关主题

- [[MySQL/10-Operations/权限与账号-Privilege|权限与账号 Privilege]]
- [[MySQL/10-Operations/备份恢复-Backup-Restore|备份恢复 Backup Restore]]
