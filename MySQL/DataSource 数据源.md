---
title: DataSource 数据源
description: Spring Boot 中 DataSource 的职责、配置项和项目实践
tags:
  - MySQL
  - DataSource
  - Spring-Boot
  - JDBC
category: MySQL
---

# DataSource 数据源

DataSource 是 Java 应用访问数据库的连接入口。Spring Boot 会根据 `spring.datasource.*` 配置和数据库驱动自动创建数据源，供 MyBatis、事务管理器等组件使用。

## 常见配置项

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/app_db?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: ${MYSQL_PASSWORD}
```

| 配置项 | 作用 |
|--------|------|
| `driver-class-name` | JDBC 驱动类 |
| `url` | 数据库连接地址 |
| `username` | 数据库用户名 |
| `password` | 数据库密码 |

## Big-event 项目经验

Big-event 展示了典型的 Spring Boot + MySQL 配置方式：

- 数据库连接参数放在 `application.yml`。
- MySQL 8 使用 `com.mysql.cj.jdbc.Driver`。
- MyBatis Mapper 层不直接管理连接，由 Spring Boot 数据源统一提供。

## 常见边界

DataSource 负责连接数据库，不负责业务逻辑。

| 内容 | 应该放在哪里 |
|------|--------------|
| 数据库连接地址 | 配置文件或环境变量 |
| SQL 语句 | Mapper |
| 业务判断 | Service |
| HTTP 参数处理 | Controller |

## 实战注意点

- 生产环境密码应通过环境变量、配置中心或密钥管理服务注入。
- 如果配置值需要强校验，可以结合 `@ConfigurationProperties` 和 Bean Validation。
- 数据源配置错误通常会在应用启动或第一次访问数据库时暴露。

## 相关主题

- [[MySQL/JDBC URL]]
- [[MySQL/MyBatis 数据访问]]
- [[Java/Framework/Spring-Boot/Learning/05-接入-Mybatis]]
- [[Java/Framework/Spring-Boot/Learning/04-yaml-配置文件的书写与获取\|yaml 配置文件的书写与获取]]
- [[Java/Advanced/多线程]]

## 参考资料

- [Spring Boot Data SQL Features](https://docs.spring.io/spring-boot/reference/data/sql.html)
- [Spring Boot Externalized Configuration](https://docs.spring.io/spring-boot/reference/features/external-config.html)
