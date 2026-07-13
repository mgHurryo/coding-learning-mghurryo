---
title: Spring-Boot 自动配置的原理
description: Spring Boot 自动配置流程解析，从 @SpringBootApplication 到 .imports 文件
tags:
  - Spring
  - Spring-Boot
  - 自动配置
  - 原理
category: Spring-Boot
---

# 09. Spring-Boot 自动配置的原理

[[08-Bean-的条件引入|上一章：Bean 的条件引入]]

## 1. 在主启动类上添加 `@SpringBootApplication` 注解

这个注解组合了 `@EnableAutoConfiguration`、`@ComponentScan`、`@SpringBootConfiguration`

---

## 2. `@EnableAutoConfiguration` 内部通过 `@Import` 导入

```
AutoConfigurationImportSelector.class
```

它会调用 `selectImports()` 方法

---

## 3. `selectImports()` 方法的核心流程

会做以下几件事：

- 读取 `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` 文件  
    （Spring Boot 2.7 以前读取 `spring.factories`）
- 获取所有自动配置类的全类名
- 返回这些配置类列表交给 Spring 容器

---

## 4. Spring 容器进入 refresh 阶段后

开始处理这些自动配置类：

- 将所有 AutoConfiguration 类作为候选配置加载进容器
- 解析 `@Configuration` 配置类

---

## 5. 执行条件装配（核心）

Spring 会解析并执行：

```
@Conditional 及其衍生注解
```

具体筛选逻辑可以查看 [[08-Bean-的条件引入]] 

例如：

- `@ConditionalOnClass`（classpath 有该类才生效）
- `@ConditionalOnMissingBean`（容器中没有 Bean 才创建）
- `@ConditionalOnProperty`（配置文件满足条件才生效）

---

## 6. 满足条件的配置生效流程

当条件成立时：

- 注册 BeanDefinition 到 IOC 容器
- 在后续 refresh 阶段实例化 Bean
- 注入到 Spring 容器中

---

## 7. 最终结果

Spring Boot 完成自动装配：

- 自动创建所需 Bean
- 自动配置 MVC / MyBatis / DataSource 等组件
- 用户无需手动 XML 配置