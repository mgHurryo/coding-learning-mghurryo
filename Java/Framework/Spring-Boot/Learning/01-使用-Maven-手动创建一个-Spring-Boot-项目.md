---
title: 使用 Maven 手动创建一个 SpringBoot 项目
description: 从零开始用 Maven 手动搭建 Spring Boot Web 项目的完整步骤
tags:
  - Spring
  - Spring-Boot
  - Maven
  - 入门
category: Spring-Boot
---

# 01. 使用 Maven 手动创建一个 SpringBoot 项目

[[02-Spring-Boot的学习路径|下一章：Spring-Boot 的学习路径]]

>初入Spring(只使用Web)

1. 首先生成一个Maven项目, 需要确保使用的Java版本符合Spring要求
2. 在生成以后, 在pom.xml中的 `<modelVersion>` 与 `<groupId>` 之间添加
```pom.xml
	<artifactId>spring-boot-starter-parent</artifactId>
```
在这之后, IDE会自动生成 `<groupId>` 和 `<version> `这两个tag, 需要确定好当前使用的 parent 版本是多少. 在这里使用的 Maven版本是 4.1.0, 使用 parent 的版本是 3.1.2.
3. 在 `<dependencies>` 中加入
```pom.xml
	<dependency><artifactId>spring-boot-starter-web</artifactId></dependency>
```   
在这之后, IDE会自动生成 `<groupId>` tag.
4. 在创建的项目下, 例如 `src/main/java/top.hurrysite` 下创建一个软件包, 在其中写上自己的逻辑, 并声明接口即可
5. 编译项目, 默认端口是8080, 此时访问 `localhost:8080/接口` 即可看到输出结果
