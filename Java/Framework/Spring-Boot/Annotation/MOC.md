---
title: Spring-Boot 注解 MOC
description: Spring Boot 开发中常用注解的汇总索引页
tags:
  - Spring
  - Spring-Boot
  - 注解
category: Spring-Boot
---

# Spring Boot Annotation MOC

本目录汇总 Spring Boot 开发中常用注解，并把注解和项目实践场景连接起来。

## 启动与配置

| 注解 | 作用 | 相关实践 |
|------|------|----------|
| [@SpringBootApplication](@SpringBootApplication.md) | Spring Boot 启动入口、自动配置、组件扫描 | [自动配置原理](../Learning/Start/09-Spring-Boot自动配置的原理.md) |
| [@Configuration](@Configuration.md) | 声明配置类，配合 `@Bean` 注册组件 | [Bean 对象的注册](../Learning/Start/07-Bean-对象的注册.md) |
| [@Autowired](@AutoWired%28%29.md) | 依赖注入 | [Bean 对象的注册](../Learning/Start/07-Bean-对象的注册.md) |

## Web 接口

| 注解 | 作用 | 相关实践 |
|------|------|----------|
| [@RestController](@RestController%28%29.md) | REST API 控制器，返回 JSON 数据 | [RESTful API 与参数校验](../Learning/Start/10-RESTful-API与参数校验.md) |
| [@RequestMapping](@RequestMapping%28%29.md) | 请求路径与方法映射 | [HTTP Method 选择指南](../../../../Network/HTTP/Guide/HTTP-Method-选择指南.md) |

## 项目经验

Big-event 中这些注解形成了一条典型链路：

```text
@SpringBootApplication
  -> 扫描 @RestController / @Service / @Configuration
  -> Controller 通过 @RequestMapping 接收 HTTP 请求
  -> Service / Mapper 通过依赖注入协作
  -> 配置类通过 @Configuration 注册安全过滤链等 Bean
```

## 相关主题

- [Spring Boot MOC](../MOC.md)
- [Spring 的一般项目结构](../../Spring-的一般项目结构.md)
- [Java 反射](../../../Advanced/反射.md)

