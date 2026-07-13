---
title: Bean 对象的扫描
description: Spring Boot 中 Bean 扫描的两种方式：XML 标签与 @ComponentScan 注解
tags:
  - Spring
  - Spring-Boot
  - Bean
  - ComponentScan
category: Spring-Boot
---

# 06. Bean 对象的扫描

[上一章：接入 Mybatis](05-接入-Mybatis.md) | [下一章：Bean 对象的注册](07-Bean-对象的注册.md)

在 Spring 中, 有两种 Bean 对象的扫描方式:

1. 标签法: `<context:component-scan base-package="top.hurry"/>`
2. 注解法: `@CompnentScan(basePackage="top.hurry")`

## 标签法: `<context:component-scan base-package="top.hurry"/>`

此方法较旧, 新的 Spring Boot 项目一般都是用注解法, 而并非此方法, 但是对于一些大型项目, 优化启动速度时, 可能会使用此方法精确控制扫描范围.

此标签写在 `/resources/applicationConttext.xml` 文件下

## 注解法: `@ComponentScan(basePackage="top.hurry")`

在之前的方法中, 我们使用 `@SpringBootApplication` 的标签来启动项目, 并且也能访问成功, 但是我们并没有添加 `@ComponentScan` 这个标签, 这是因为, 在源码中, `@SpringBootApplication` 这个标签是有三个标签的:

```Java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
public @interface SpringBootApplication {}
```

所以, `@SpringBootApplication` 底层也是在使用 `@ComponentScan` 在扫描包的

`@ComponentScan` 在没有变量传入的时候, 它自动会使用当前标记的类的当前包来作为参数传入, 扫描当前包及其子包, 也就是说, Spring Boot 在启动时会默认扫描启动类以及启动类下的子包

在实际开发中, 所有需要被Spring 管理的 Bean 对象, 我们一律都写在启动类所在包的子包中. 例如 Service 层, DAO 层, Controller 层.

## 与项目实践的连接

Big-event 中，启动类位于父包下，Controller、Service、Config、Filter、Mapper 等包都在其子包中，便于组件扫描。

需要注意：

- 普通 Spring 组件依赖 `@ComponentScan`。
- MyBatis Mapper 通常还需要 `@Mapper` 或 `@MapperScan`，见 [接入 MyBatis](05-接入-Mybatis.md)。
- 包结构设计见 [Spring-的一般项目结构](../../../Spring-的一般项目结构.md)。

## 相关主题

- [@SpringBootApplication](../../Annotation/@SpringBootApplication.md)
- [@Autowired](../../Annotation/@AutoWired%28%29.md)
- [反射](../../../../Advanced/反射.md)
