---
title: MySQL MOC
description: MySQL、JDBC、DataSource 与 Java 持久层实践索引
tags:
  - MySQL
  - JDBC
  - MOC
  - 索引
category: MySQL
---

# MySQL MOC

> 本目录沉淀数据库连接、JDBC URL、数据源配置和 MyBatis 数据访问的通用知识。Big-event 项目的数据库经验会分散到这些正式主题中，而不是只保留在项目总结里。

## 数据库连接

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/JDBC URL\|JDBC URL]] | Java 连接 MySQL 的 URL 结构、参数和常见注意事项 |
| [[MySQL/DataSource 数据源\|DataSource 数据源]] | Spring Boot 中数据库连接配置和数据源职责 |

## Java 持久层

| 笔记 | 说明 |
| :--- | :--- |
| [[MySQL/MyBatis 数据访问\|MyBatis 数据访问]] | Mapper、注解 SQL、参数绑定、驼峰映射 |
| [[Java/Framework/Spring-Boot/Learning/05-接入-Mybatis\|Spring Boot 接入 MyBatis]] | 在 Spring Boot 项目中集成 MyBatis |

## 项目经验

Big-event 项目中体现的通用经验：

- 使用 `spring.datasource.*` 交给 Spring Boot 自动配置数据源。
- 使用 MySQL 8 驱动 `mysql-connector-j`。
- 使用 `@MapperScan` 或 `@Mapper` 注册 Mapper。
- 使用 `#{}` 预编译参数绑定，避免 SQL 注入。
- 使用 `map-underscore-to-camel-case` 解决数据库下划线字段和 Java 驼峰字段映射。

## 相关主题

- [[Home\|返回主页]]
- [[Java/Framework/MOC\|Java 框架 MOC]]
- [[Java/Framework/Spring-Boot/MOC\|Spring Boot MOC]]
- [[Java/Framework/Spring-的一般项目结构\|Spring 的一般项目结构]]
- [[Network/HTTP/Guide/幂等接口设计\|幂等接口设计]]
