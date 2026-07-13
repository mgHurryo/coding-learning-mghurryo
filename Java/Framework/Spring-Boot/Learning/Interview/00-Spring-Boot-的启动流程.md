---
title: Spring Boot 的启动流程
description: SpringApplication 启动流程分析，从 new SpringApplication() 到 refresh 容器的完整过程
tags:
  - Spring-Boot
  - Interview
  - 启动流程
category: Spring-Boot
---
# Spring Boot 的启动流程

Spring Boot 的启动设计两个核心的 API:

- `new SpringApplication()`
	1. 确认 web 引用的类型
	2. 加载 `ApplicationContextInitializer` (初始化器)
	3. 加载 `ApplicationListener` (监听器)
	4. 记录主启动类 (只有把主启动类记录下来我们才能扫描主启动类所在的包及其子包下其他的类)
- `run()`
	1. 准备环境对象 `Enviroment`, 用于加载系统属性等等
	2. 打印 `Banner`
	3. 实例化容器 `Context`
	4. 准备容器, 为容器设置 `Enviroment`, `BeanFactoryPostProcessor`, 并加载主类对应的 `BeanDefinition`
	5. **刷新容器** (创建所有 Bean实例)
	6. 返还容器

## 具体怎么说

Spring Boot 的启动流程, 本质就是加载各种配置信息, 然后还初始化 IOC 容器并返回.

在启动中会做这么几个事情:

首先, 在启动 `springApplication.run` 这行代码的时候, 在他的方法内部其实会做两个事情:
1. 创建 `SpringApplication` 对象;
2. 执行 `run` 方法

其次, 在创建 `SpringApllication` 对象的时候,在他的构造方法内部主要做3个事情
1. 确认 web 应用类型, 一般情况下是 `Servlet` 类型, 这种类型的应用, 将来会自动启动一个 `tomcat`
2. 从 `spring.factories` 配置文件中, 加载默认的 `ApplicationContextInitializer` 和 `ApplicationListener`
3. 记录当前引用的主启动类, 将来做包扫描使用

最后, 对象创建好了以后, 再调用改对象的 `run` 方法, 在 `run` 方法的内部主要做4个事情
1. 春被 `Enviroment` 对象, 其中会封装一下当前应用运行环境的参数, 比如环境变量灯灯
2. 实例化容器, 这里仅仅是创建`ApplicationContext` 对象
3. 容器创建好了以后, 会为容器做一下准备工作, 比如为容器设置 `Enviroment`, `BeanFactoryPostProcessor` 后置处理器, 并且加载主类对应的 `Definition`
4. 刷新容器, 在这里会真正的创建 `Bean` 实例

总结一下, 其实 `SpringBoot` 启动的时候核心就两步, 创建 `SpringApplication` 对象以及 `run` 方法的调用, 在 `run` 方法中会真正的实例化容器, 并创建容器中需要的 `Bean` 实例, 最终返回