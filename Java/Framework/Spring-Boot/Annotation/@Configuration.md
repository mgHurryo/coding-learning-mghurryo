---
title: "@Configuration"
---
# `@Configuration`

`@Configuration` 是一个用于声明“配置类”的注解, 标识该类为 Spring 容器的**显式 Bean 定义源（Bean Definition Source）**, 用于替代 XML 配置。

简单点说,  在一个类上标记这个注解,  这个类就变成配置类, 如果该配置类位于 Spring Boot 启动类所在包及其子包下,  它会被 `@ComponentScan` 自动扫描到。Spring 启动时会解析该配置类中的配置信息, 例如 `@Bean`、`@Import`、`@ComponentScan` 等, 并将其中声明的 Bean 注册到 Spring 容器中统一管理。

## 关联知识

- [Bean 对象的注册](../Learning/Start/07-Bean-对象的注册.md)
- [yaml 配置文件的书写与获取](../Learning/Start/04-yaml-配置文件的书写与获取.md)
- [Spring Security JWT 无状态认证](../Learning/Start/12-Spring-Security-JWT无状态认证.md)
- [DataSource 数据源](../../../../MySQL/12-Java-Persistence/DataSource-数据源.md)

