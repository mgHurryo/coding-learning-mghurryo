---
title: JDBC URL
description: Java 连接 MySQL 时 JDBC URL 的结构、参数和常见配置
tags:
  - MySQL
  - JDBC
  - URL
category: MySQL
---

# JDBC URL

JDBC URL 是 Java 应用连接数据库时使用的连接地址。它描述了使用哪种数据库协议、连接哪台主机、哪个端口、哪个数据库，以及附加连接参数。

## 基本结构

```text
jdbc:mysql://host:port/database?param1=value1&param2=value2
```

| 部分 | 说明 |
|------|------|
| `jdbc:mysql://` | 使用 MySQL JDBC 驱动 |
| `host` | 数据库主机，例如 `localhost` 或数据库服务地址 |
| `port` | 数据库端口，MySQL 默认是 `3306` |
| `database` | 要连接的数据库名 |
| 查询参数 | 字符集、时区、SSL 等连接选项 |

## Big-event 项目经验

Big-event 使用的是本地 MySQL 数据库连接。这里抽象出的通用经验是：

- 数据库地址和账号密码属于环境配置，不应该硬编码在业务类中。
- Spring Boot 项目应优先使用 `application.yml` 或环境变量管理连接配置。
- 中文数据场景下需要关注字符集参数，避免写入或读取乱码。

## 常见参数

| 参数 | 作用 |
|------|------|
| `useUnicode=true` | 使用 Unicode 字符集处理数据 |
| `characterEncoding=UTF-8` | 指定字符编码 |
| `serverTimezone=Asia/Shanghai` | 指定数据库连接时区 |
| `useSSL=false` | 本地开发常见关闭 SSL，生产环境需按安全要求配置 |

## 实战注意点

- 不要在公开笔记或仓库中暴露真实数据库密码。
- 本地开发可以使用 `localhost`，部署环境应改为数据库服务地址。
- 多环境项目建议拆分为 `application-dev.yml`、`application-prod.yml` 等配置。

## 相关主题

- [DataSource 数据源](DataSource-数据源.md)
- [03-Spring-Boot配置文件](03-Spring-Boot配置文件.md)
- [04-yaml-配置文件的书写与获取](04-yaml-配置文件的书写与获取.md)
- [网络编程](网络编程.md)
- [网络传输层](Network/Transport/MOC.md)

## 参考资料

- [Spring Boot Externalized Configuration](https://docs.spring.io/spring-boot/reference/features/external-config.html)
- [MySQL Connector/J Developer Guide](https://dev.mysql.com/doc/connector-j/en/)

