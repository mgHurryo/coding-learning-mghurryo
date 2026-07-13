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
| [[Java/Framework/Spring-Boot/Annotation/@SpringBootApplication\|@SpringBootApplication]] | Spring Boot 启动入口、自动配置、组件扫描 | [[09-Spring-Boot自动配置的原理\|自动配置原理]] |
| [[Java/Framework/Spring-Boot/Annotation/@Configuration\|@Configuration]] | 声明配置类，配合 `@Bean` 注册组件 | [[07-Bean-对象的注册\|Bean 对象的注册]] |
| [[Java/Framework/Spring-Boot/Annotation/@AutoWired()\|@Autowired]] | 依赖注入 | [[07-Bean-对象的注册\|Bean 对象的注册]] |

## Web 接口

| 注解 | 作用 | 相关实践 |
|------|------|----------|
| [[Java/Framework/Spring-Boot/Annotation/@RestController()\|@RestController]] | REST API 控制器，返回 JSON 数据 | [[10-RESTful-API与参数校验\|RESTful API 与参数校验]] |
| [[Java/Framework/Spring-Boot/Annotation/@RequestMapping()\|@RequestMapping]] | 请求路径与方法映射 | [[Network/HTTP/Guide/HTTP-Method-选择指南\|HTTP Method 选择指南]] |

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

- [[Java/Framework/Spring-Boot/MOC\|Spring Boot MOC]]
- [[Java/Framework/Spring-的一般项目结构\|Spring 的一般项目结构]]
- [[Java/Advanced/反射\|Java 反射]]

