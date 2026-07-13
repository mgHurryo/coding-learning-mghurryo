---
title: Bean 对象的注册
description: Spring Boot 中通过 @Bean、@Import 等方式注册 Bean 的完整说明
tags:
  - Spring
  - Spring-Boot
  - Bean
  - Import
  - Configuration
category: Spring-Boot
---

# 07. Bean 对象的注册

[[06-Bean-对象的扫描|上一章：Bean 对象的扫描]] | [[08-Bean-的条件引入|下一章：Bean 的条件引入]]

`Bean` 的组件扫描注册 Bean:

|      注解       |      说明       |                       位置                        |
| :-----------: | :-----------: | :---------------------------------------------: |
| `@Component`  | 声明 Bean 的基本注解 |                   不属于以下三类时使用                    |
| `@Controller` |   第一注解的衍生注解   |                标注在 Controller 类上                |
|  `@Service`   |   第一注解的衍生注解   |              标注在 Services (业务) 类上               |
| `@Repository` |   第一注解的衍生注解   | 标注在 Repository (数据访问) 类上 (由于跟 Mybatis 整合, 用的较少) |

> 如果要注册的 Bean 对象来自第三方 (非自定义), 无法使用以上四种方法注解声明 Bean对象

可以使用的方法:

- `@Bean`
- `@Import`
- `@AutoWired`  使用 bean 对象 (依赖注入)
- Sping Boot 自动装配: 根据各种条件(依赖, 配置, 条件注解, 自动导入和创建一些 bean)

其中, `@Bean` 和 `@Import` 通常写到配置类中, 而 `@AutoWire` 则通常在业务层中, 不参与注册, 但是参与使用

## `@Bean` 注册

### 作用对象

> 方法

### 标注位置

只能写在 **`@Configuration` 配置类** 的内部方法上 (只能写在由 Spring 管理的类中)

```Java

@Configuration
public class AppConfig {
	@Bean(...)
	public AClass AClassName() {
		// 装配
		return AClass;
	}
}
```

### 使用的场景

- 精确控制一个第三方类的创建过程
- 需要在创建的时候控制第三方类的属性, 调用初始化方法, 根据条件来决定创建哪个实现类.  (生产环境与开发环境调用的具体实现类不同)
- 使用的对象是一个单例 (在内存中只存一个该 Bean 对象)

一般使用 `@Bean` 的写法是:

```Java

// 假如我们有一个第三方类, 且我们无法修改其中的值
public class Student {
	private String name;
	private int age;
	private int grade;
	
	getter and setter...;
	
	public void start() {}
	
	public void destroy() {}
	
	public Student() {}
	
	public Student(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	public Student(String name, int age, int grade) {
		this.name = name;
		this.age = age;
		this.grade = grade;
	}
}
```

我们想要用 Spring 管理这个对象, 同时遵循配置文件中的类型, 这里有三种 Bean 写法:

第一种 (标准的Bean写法, 可能会有空指针的风险, 建议校验):

```Java

@Bean(initMethod = "start", destroyMethod = "destroy")
public Student student() {
	Student stu = new Student();
	stu.setName("aaa");
	stu.setAge(12);
	stu.setGrade(99);
	return stu;
}
```

第二种和第三种的前置条件 (这里需要用到 [[03-Spring-Boot配置文件|配置文件]] 和 [[04-yaml-配置文件的书写与获取|获取配置文件内容]] 的知识):

```Java
@Configuration
public class MyConfig {
	@Value("${student.name}")
	private String name;
	@Value("${student.age}")
	private int age;
	@Value("${student.grade}")
	private int grade;
	
	@Bean(...)
	public Student student() {...} // 后续几种都写这里
}
```

第二种 (有部分必填数据时的Bean写法):

```Java

@Bean(initMethod = "start", destroyMethod = "destroy")
public Student student() {
	Student stu = new Student(name, age);
	stu.setGrade(99);
	return stu;
}
```

第三种 (直接全参构造):

```Java

@Bean(initMethod = "start", destroyMethod = "destroy")
public Student student() {
	Student stu = new Student(name, age, grade);
	return stu;
}
```

### 重要

> 普通业务数据对象，比如 Student、User、Order，一般不应该注册成单例 Bean。
> 因为它们通常代表一份具体数据，每次请求、每次查询都可能不同。
> Bean 更适合放 Service、Controller、DAO、工具组件、配置对象、第三方客户端等长期复用的对象。
> 
> 单例 Bean 创建完成后，如果还在运行过程中保存会变化的业务数据，就容易线程不安全。
> 初始化阶段设置固定属性通常没问题，危险的是把请求数据、用户数据、订单数据等可变状态放进单例 Bean 里。
>
> 开发原则是, 能使用 **单例** 则使用 **单例**, 避免线程不安全发生, 如果一定需要使用多例, 则做好 **数据隔离**, 例如避免多线程修改数值, 添加锁, 设置 `ThreadLocal` 等等方式.


## `@Import` 注册

### 作用对象

> 类
### 标注位置

任意配置类或者启动类上

```Java

@Import({ClassName1.class, ClssName2.class, ClassName3.class})
public ...
```

### 使用的场景

直接导入有 `@Component`,  `@Service`,  `@Repository`,  `@Controller`,  `@Configuration` 标签的类, 也可导入普通类.

>**注意:**
>
>导入普通类时，Spring 会尝试把它注册成 Bean。它不一定需要空参构造，但构造方法参数必须能从 Spring 容器中找到；否则会在 Spring 启动时运行时报错。


导入 `@Component`,  `@Service`,  `@Repository`,  `@Controller`  (一般不常用, 都是使用 `@ComponentScan` 直接导入):

```Java
@Service
public class UserServices {}
```

```Java
@Import(UserServices.class)
public class SpringbootRegisterApplication {}
```

导入 `@Configuration` 也是同理, 直接导入即可

如果需要多个导入, 可以使用数组:

```Java
@Import({UserServices.class, OrderServices.class})
```

但是这样不是很优雅, 所以当遇见动态导入或者导入多个类的时候, 通常会加入 `@ImportSelector` :

```Java

public class ConfigSelector implements ImportSelector {
	@Override
	public String[] selectImports(AnnotationMetadata metadata) {
		return new String[]{
			"com.example.config1",
			"com.example.config2"
		};
	}
}
```

```Java
@Import(ConfigSelector.class)
public class springbootRegisterApplication {}
```

看调用 `@Import` 的类的性质导入:

```Java

// 此处为看调用 import 的类的名字来决定导入谁
public class ConfigSelector implements ImportSelector {
	@Override
	public String[] selectImports(AnnotationMetadata metadata) {
		String motherClassName = metadata.getClassName();
		if ("com.example.config.ConfigWay1".equals(motherClassName)) {
			return new String[]{
				"com.example.config1",
				"com.example.config2"
			};
		} else if ("com.example.config.ConfigWay2".equals(motherClassName)) {
			return new String[]{
				"com.example.config3",
				"com.example.config4"
			}
		} else {
			return new String[]{};
		}
	}
}
```

自己在文件中写好需要导入的库 (需要自己手动解析文件) [[#^ssb-7-1|详解]]:

```Java

public class ConfigSelector implements ImportSelector {
	@Override
	public String[] selectImports(AnnotationMetadata metadata) {
		ArrayList<String> al = new ArrayList<>();
		InputStream is = ConfigSelector.class
				.getClassLoader()
				.getResourceAsStream("common.imports");
		BufferedReader br = new BufferedReader(
				new InputStreamReader(is)
			);
		String line = null;
		try {
			while ((line = br.readLine()) != null) {
				al.add(line);
			}
		} catch (Exception error) {
			throw new RuntimeException(error.getMessage());
		} finally {
			try {
				br.close();
			} catch (Exception error) {
				throw new RuntimeException(error.getMessage());
			}
		}
		return al.toArray(new String[0]);
	}	
}
```

在 `common.imports` 文件中一行一行的写配置:

```txt
com.example.config1
com.example.config2
```

这几种方法之后都是使用下面的方法来导入配置:

```Java
@Import(ConfigSelector.class)
public class ... // 配置类或者启动类
```

### 备注

#### 使用自定义文件导入的详细解析 ^ssb-7-1

在上面使用自己的配置文件来进行导入库时, 我们写了这个:

```Java
InputStream is = ConfigSelector.class
		.getClassLoader
		.getResourceAsStream("common.imports");
```

详解:
1. `configSelector.class` 获得该配置类的 class 对象
2. `.getClassLoader` 拿到加载这个类的类加载器. 这里主要是为了让类加载器帮我们从 `classpath` 中找资源文件
3. `.getResourceAsStream()` 从 `classpath` 根目录下找 `common.imports` 文件. 如果找到, 就打开成 `InputStream`, 如果找不到, 就返回 null

最后我们把他封装为一个 `bufferedReader`, 方便我们一行一行读取.

## 与项目实践的连接

Bean 注册不是只服务于学习示例，在真实 Spring Boot 项目中经常用于：

- 注册 `SecurityFilterChain`，见 [[12-Spring-Security-JWT无状态认证\|Spring Security JWT 无状态认证]]。
- 注册密码加密器，见 [[Security/Authentication/密码存储实践]]。
- 注册第三方 SDK 客户端。
- 通过配置类组织跨模块组件。

Big-event 中的安全配置类就是典型的“配置类 + Bean 注册 + 依赖注入”组合。

## 相关主题

- [[Java/Framework/Spring-Boot/Annotation/@Configuration\|@Configuration]]
- [[Java/Framework/Spring-Boot/Annotation/@AutoWired()\|@Autowired]]
- [[Java/Advanced/反射]]
