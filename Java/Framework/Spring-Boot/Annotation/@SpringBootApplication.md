---
title: "@SpringBootApplication"
---
# `@SpringBootApplication`

这是 Spring Boot 的启动标识, 其本质为一个配置类, 其包含了`@SpringBootConfiguration`,  `@EnableAutoConfiguration`, `@ComponentScan`, 相同于, 我是一个配置类, 同时开启自动配置与组件扫描. 

自动配置的具体原理可以前往 [[09-Spring-Boot自动配置的原理]] 查看

组件扫描具体原理可以前往 [[06-Bean-对象的扫描]] 查看

## 容易踩的坑

1. 没有添加 `@MapperScan()`: 如果只在 Mapper 对象上申明 `@Mapper`, Spring 并不会扫描到, 必须手动添加 `@MapperScan("top.hurry.XXX Mapper")`
2. 启动类必须放到父包中: 如果不放到父包中, 则会无法扫描到 Controller, Service 等包.

## 关联知识

- [[Java/Framework/Spring-Boot/Learning/06-Bean-对象的扫描\|Bean 对象的扫描]]
- [[Java/Framework/Spring-Boot/Learning/09-Spring-Boot自动配置的原理\|Spring Boot 自动配置的原理]]
- [[Java/Framework/Spring-Boot/Learning/05-接入-Mybatis\|接入 MyBatis]]
- [[Java/Advanced/反射\|反射]]
- [[Java/Framework/Spring-的一般项目结构\|Spring 的一般项目结构]]


