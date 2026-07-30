---
title: Spring Boot MOC
description: Spring 与 Spring Boot 主题笔记的索引地图
tags:
  - Spring-Boot
  - MOC
  - 索引
category: Java
---

# Spring Boot MOC

本目录汇总 Spring Boot 相关的学习笔记。

## Spring-Boot 系列

| 序号  | 笔记                                                                   | 标签                                                        | 说明                        |
| :-- | :------------------------------------------------------------------- | :-------------------------------------------------------- | :------------------------ |
| 1   | [使用 Maven 手动创建一个 SpringBoot 项目](Learning/Start/01-使用-Maven-手动创建一个-Spring-Boot-项目.md) | #Spring #Spring-Boot #Maven #入门                           | 手动搭建 Spring Boot Web 项目   |
| 2   | [Spring-Boot 的学习路径](Learning/Start/02-Spring-Boot的学习路径.md)                | #Spring #Spring-Boot #学习路径 #索引                            | 基础、实战、面试三阶段路线             |
| 3   | [Spring-Boot 配置文件](Learning/Start/03-Spring-Boot配置文件.md)                  | #Spring #Spring-Boot #配置文件 #yaml #properties              | properties 与 yaml 配置文件对比  |
| 4   | [yaml 配置文件的书写与获取](Learning/Start/04-yaml-配置文件的书写与获取.md)                              | #Spring #Spring-Boot #yaml #配置读取 #ConfigurationProperties | yaml 语法与两种取值方式            |
| 5   | [接入 Mybatis](Learning/Start/05-接入-Mybatis.md)                                        | #Spring #Spring-Boot #Mybatis #MySQL                        | 依赖、Mapper 扫描、数据源与 SQL 映射 |
| 6   | [Bean 对象的扫描](Learning/Start/06-Bean-对象的扫描.md)                                        | #Spring #Spring-Boot #Bean #ComponentScan                 | XML 标签与 @ComponentScan 扫描 |
| 7   | [Bean 对象的注册](Learning/Start/07-Bean-对象的注册.md)                                        | #Spring #Spring-Boot #Bean #Import #Configuration         | @Bean、@Import 注册 Bean 详解  |
| 8   | [Bean 的条件引入](Learning/Start/08-Bean-的条件引入.md)                                        | #Spring #Spring-Boot #Bean #Conditional #条件注解             | @Conditional 系列条件注解       |
| 9   | [Spring-Boot 自动配置的原理](Learning/Start/09-Spring-Boot自动配置的原理.md)            | #Spring #Spring-Boot #自动配置 #原理                            | 自动配置流程解析                  |
| 10  | [常用的 Spring-Boot 注解](Annotation/MOC.md)            | #Spring #Spring-Boot #注解 #待补充                             | 常用注解汇总（待补充）               |
| 11  | [RESTful API 与参数校验](Learning/Start/10-RESTful-API与参数校验.md) | #Spring #Spring-MVC #REST #Validation | Controller、DTO、参数绑定、Bean Validation |
| 12  | [全局异常处理与统一响应](Learning/Start/11-全局异常处理与统一响应.md) | #Spring #Spring-Boot #Exception #API | @RestControllerAdvice、统一响应模型 |
| 13  | [Spring Security JWT 无状态认证](Learning/Start/12-Spring-Security-JWT无状态认证.md) | #Spring-Security #JWT #Authentication | SecurityFilterChain 与 JWT Filter |
| 14  | [Lombok 与 DTO 模式](Learning/Start/13-Lombok与DTO模式.md) | #Lombok #DTO #Architecture | DTO/POJO 边界与样板代码简化 |

## 项目案例

| 项目 | 说明 |
| :--- | :--- |
| [Big-event 项目知识索引](Projects/Big-event/00-知识索引.md) | Spring Boot + Security + MyBatis + MySQL + JWT 的案例分析 |

## 相关主题

- [返回主页](../../../Home.md)
- [Java MOC](../../Foundation/MOC.md)
- [Spring 项目结构](../Spring-的一般项目结构.md)
- [MySQL MOC](MySQL-MOC.md)
- [Security MOC](../../../Security/MOC.md)
