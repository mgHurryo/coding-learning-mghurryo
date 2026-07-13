---
title: Spring Boot 的启动流程
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