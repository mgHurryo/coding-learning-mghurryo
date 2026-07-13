---
title: IOC 容器初始化流程
description: AbstractApplicationContext.refresh() 方法详解，从 BeanFactory 准备到单例预实例化的完整流程
tags:
  - Spring-Boot
  - Interview
  - IOC
category: Spring-Boot
---
# IOC 容器初始化流程

主代码: `AbstractApplicationContext.refreash()`
流程:
1. 准备 `BeanFactory(DsfaultListableBeanFactory`
	- 设置 `ClassLoader`
	- 设置 `Enviroment`
2. 扫描要放入容器中的 `Bean`, 得到对应的 `BeanDefinition` (只扫描不创建)
3. 注册 `BeanPostProcessor`
4. 处理国际化
5. 初始化时间多播器 `ApplicationEventMulticaster`
6. 启动 `tomcat`
7. 绑定时间监听器和时间多播器
8. 实例化非懒加载的单例 `Bean` 

## 具体怎么说

IOC 容器的初始化, 核心工作实在 `AAbstractApplicationContext.refresh` 方法中完成的

在 `refresh` 方法中主要做了这么几件事
1. 准备`BeanFactory`, 在这一块需要给 `BeanFactory` 设置很多属性, 比如说类加载器, `Environment` 等
2. 执行 `BeanFactory` 后置处理器, 这一阶段会扫描要放入到容器中的 `Bean` 信息, 得到对饮的 `BeanDefinition` (只扫描, 不创建)
3. 注册 `BeanPostProcessor`, 我们自定义的 `BeanPostProcessor` 就是在这个阶段被加载的.
4. 启动 `tomcat`
5. 实例化容器中非懒加载的单例 `Bean` (多例 `Bean` 还有懒加载的 `Bean` 不会在这个阶段实例化, 将来用到的时候再创建)
6. 当容器化初始化完毕后, 做 扫尾工作, 例如清除缓存等等

总结一下, 在 IOC 容器初始化的过程中, 首先得准备并执行 `BeanFactory` 后置处理器,其次得注册  `Bean` 后置处理器并启动 `tomcat`, 最后借助于 `BeanFactory` 完成 `Bean` 的初始化.

