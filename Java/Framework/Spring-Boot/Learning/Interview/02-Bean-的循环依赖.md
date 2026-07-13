---
title: Bean 的循环依赖
---
# Bean 的循环依赖

指的是依赖闭环, 例如:

```text
	
	|-------->-------|
	|     A依赖B      |
Bean A             Bean B
	|     B依赖A      |
	|--------<-------|

```

此时, 如果配置文件中没有配置允许循环依赖, 

```yaml
# 循环依赖配置
spring: 
	main: 
		allow-circular-references: true # 允许循环依赖(false 则是不允许)
```

那么, 在启动阶段则会报错.

如果配置为 `true`, 那么 Spring 则会开始处理循环依赖问题. 

这个配置真正的含义不是无视循环依赖, 而是尝试讲一个没有完全初始化好的 `Bean` 对象, 提前借给另一个使用.

整体涉及到的有:

- 三层缓存(完全体Bean池, 半成品Bean池, Bean提前引用工厂)
	- 完全体Bean池: 已经完整创建完成的单例 Bean(正式成品仓库)
	- 半成品 Bean 池: 提前暴露的 Bean 引用(大部分情况下不会进入, 在循环引用中会进入)
	- Bean 引用工厂: 获取 Bean 提前引用的小工厂

如果有两个Bean, A 和 B, Spring 首先扫描并开始注入的是 A, 那么整体流程是:
1. 创建 A 空对象
2. 此时创建一个 A 的小工厂, 负责提前暴露引用 A
3. 在 Bean 的穿管流程中注入各个数据, 发现此时需要 B 对象
4. 暂停 A Bean 的创建
5. 开始创建 B 的空对象
6. 此时创建一个 B 的小工厂, 负责提前暴露引用 B
7. 在 Bean 的创建流程中注入各个数据, 发现此时需要 A 对象
8. 在三层缓冲区中寻找 A
	1. 先找第一层完全体缓冲区, 无(由于此时 A 是一个半成品, 同时在前面停止了 A Bean 的创建, 所以第一层和第二层都不存在 A)
	2. 检查此时该 Bean 是否正在创建, 如果是, 则检查第二层半成品缓冲区, 无
	3. 第二层无, 同时允许提前引用, 则进入同步代码块
		1. 查找第一层缓冲区, 无
		2. 查找第二层缓冲区, 无
		3. 查找第三层缓冲区, 有
		4. 将第三层的 A 的放入第二层, 删除第三层的 A 工厂
		5. 返回 A
9. 将第二层的 A (具体是 A 本体还是 A的代理对象, 需要取决于 AOP) 注入到 B 中
10. B Bean 创建完成, 进入第一层缓冲区
11. 此时 A Bean 继续创建
12. getBean(B) 已经带着完整的 B 返回
13. 将 B 注入 A
14. 完成创建 A Bean 后, 将 A 放入第一层缓冲区, 删除第二层缓冲区的 A

## 具体怎么说

Bean 的循环依赖指的是 A 依赖 B, B 有依赖 A 这样的依赖闭环问题, 在 Spring 中, 通过三个对象缓存去来解决循环依赖问题, 这三个缓存被定义到了 `DefaultSingletonBeanRegistry` 中, 分别是 `singletonObjects` 用来存储创建完成的 Bean, `earlySingletonObjects` 用来存储未完成依赖注入的 `Bean`, 还有 `SingletonFactories` 用来存储创建 `Bean` 的 `ObjectFactory`. 假如说现在 A 依赖 B, B 依赖 A, 且 A 先被创建:

首先, 调用 A 的构造方法实例化 A, 当前 A 还没有处理依赖注入, 暂且把他称为半成品, 此时会把半成品给 A 封装到一个 `ObjectFactory` 中, 并存储到 `springFactories`  的缓存区

接下来, 要处理 A 的依赖注入, 由于此时还没有 B, 此时得先实例化一个 B, 同样的, 半成品 B 也会被封装到 `ObjectFactory` 中, 并存储到 `springFactories` 缓存区

紧接着, 要处理 B 的依赖注入, 此时会找到 `springFactories` 中 A 对应的 `ObjectFactory` 调用他的 `getObject` 方法得到刚才实例化的半成品 A (如果需要代理对象, 则会自动创建代理对象， 将来得到的就是代理对象(上方的具体创建流程写的很清楚, 可以参考上面的)) 把得到的半成品 A 注入给 B, 并同时会把半成品给 A 存入到 `earlySingletonObjects` 中, 将来如果还有其他的类循环依赖了 A, 就可以直接从那个 `earlySingletonObjects` 中找到他, 那么此时 `springFactories` 中创建 A 的`ObjecFactory` 也可以删除了

现在, B的依赖注入处理完了后, B 就创建完毕了, 就可以把 B 的对象存入到 `singletonObjects` 中了, 并同时删掉 `springFactories` 中创建 B 的 `ObjectFactory

B 创建完毕后, 就可以继续处理 A 的依赖注入了, 把 B 注入给 A, 此时 A 也创建完毕了, 就可以把 A 的对象存储到 `singletonObject` 中, 并同时删除掉 `earlySingleObjects` 中的半成品 A