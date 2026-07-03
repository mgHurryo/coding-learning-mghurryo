---
title: Spring-Boot 配置文件
description: Spring Boot 中 properties 与 yaml 配置文件的对比与使用方式
tags:
  - Spring
  - Spring-Boot
  - 配置文件
  - yaml
  - properties
category: Spring-Boot
---

# 03. Spring-Boot 配置文件

[[02-Spring-Boot的学习路径|上一章：Spring-Boot 的学习路径]] | [[04-yaml-配置文件的书写与获取|下一章：yaml 配置文件的书写与获取]]

Spring Boot 的配置文件名为 `application`, 有两种配置方法

1. `properties`配置, 文件名为 `application.properties`
2. `yaml` 配置, 文件名为 `application.yml` 或者 `application.yaml`


## `properties`配置文件

不像 `yaml` 还有 `json` 文件那样, 有明显的层级, 基本上都是直接将层级写入单条中

```applicationproperties
server.port=8080 设置服务器端口为8080
server.servlet.context-path=/start 设置起始路径为 /start
```

## `yaml` 配置文件

yaml 有两种文件后缀, 分别为 `yml` 还有 `yaml`, 两者内容相同, 仅仅后缀不同, 在正式开发中, 一般都会使用 `yml` 文件

yaml 格式具有明显的层级划分, 使用空格来控制层级

需要注意的是，值前方必须有一个空格来作为分隔符

```yml
server:
	port: 8080 设置服务器端口为8080
	servlet:
		context-path= /start 设置起始路径为 /start
```


在实际开发中, 通常使用 `yml` 配置文件, 因为层级清晰, 同时减少了大量的重复编写
