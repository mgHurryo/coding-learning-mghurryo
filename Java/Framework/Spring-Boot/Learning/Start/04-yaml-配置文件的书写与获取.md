---
title: yaml 配置文件的书写与获取
description: Spring Boot 中 yaml 配置文件的语法、数组写法以及 @Value 与 @ConfigurationProperties 取值方式
tags:
  - Spring
  - Spring-Boot
  - yaml
  - 配置读取
  - ConfigurationProperties
category: Spring-Boot
---

# 04. yaml 配置文件的书写与获取

[上一章：Spring-Boot 配置文件](03-Spring-Boot配置文件.md) | [下一章：接入 Mybatis](05-接入-Mybatis.md)

在常见开发中, 配置文件的需求一般分为两种:

1. 三方技术配置信息 (e.g. Redis, MySQL, RabbitMQ)
2. 自定义配置信息 (e.g. API token, password)

其中, 三方技术配置信息大部分都会自动读取并解析, 并不需要我们的额外去自己写解析逻辑

但是我们的自定义配置信息需要自己配置，自己写解析

## 编写 `yaml` 配置文件

在[上一章](03-Spring-Boot配置文件.md)中, 我们讲了 `yaml` 的特性, 即为有层级, 有缩进
这里以邮箱系统作为编写 `yaml` 配置文件的演示:

```yaml
email:
	user: ccaa@qq.com
	code: aaccbbaaccbbaabb
	host: stmp:qq.com
	auth: true
```

### 在 `yaml` 配置文件中使用 数组

yaml 配置文件中, 我们使用 `-` 来作为数组中每个元素的分隔符

```yaml
hobbies:
	- 打篮球
	- 打游戏
	- 打电动
```


## 获取 `yaml` 配置文件中的信息

## `@Value("$...")` 方法

Spring Boot 中, 提供了一种很方便的方法来单独获取配置文件中的信息, 格式为

```Java
// 获取并赋值
@Value("$父层级.子层级.子子层值名");
权限修饰符 数据类型 子子层值名;

// 使用数据
System.out.println(子子层值名);
```

需要注意的是, 变量中声明的子子层值名需要与配置文件中的相同, 否则无法自动赋值

以前面 email 为例子:

```Java
public class Email{
	// 获取user
	@Value("$email.user");
	public String user;
	
	// 获取code
	@Value("$email.code");
	public String code;
	
	// 获取host
	@Value("$email.host");
	public String host;
	
	// 获取auth
	@Value("$email.auth");
	public boolean auth;
}
```

### `@ConfigurationProperties(pfix="...")` 方法

这个方法可以一键获取到父层级中的所有子层级信息, 编写方法如下:

```Java
@ConfigurationProperties(prefix = "父层级名");
public class className{
	public String 子值名;
	public String 子值名;
	public boolean 子值名;
}
```

需要注意的是, 与Value方法中的变量名声明要求相同, 声明变量的子值名必须与配置文件中的子值名相同, 否则无法自动赋值

还是以前面 email 为例:

```Java
@Component
@ConfigurationProperties(prefix = "email");
public class email{
	public String user;
	public String code;
	public String host;
	public boolean auth;
}
```

## 与项目实践的连接

`@ConfigurationProperties` 适合绑定一组有共同前缀的配置，例如：

- `spring.datasource.*`：数据库连接配置，见[DataSource 数据源](DataSource-数据源.md)。
- `jwt.*`：Token 密钥、过期时间等认证配置，见 [JWT-无状态认证](../../../../../Security/Authentication/JWT-无状态认证.md)。
- 自定义第三方服务配置：邮件、对象存储、支付 SDK 等。

相比零散使用 `@Value`，配置属性类更适合中大型项目维护，也更容易配合校验和自动补全。

## 相关主题

- [@Configuration](../../Annotation/@Configuration.md)
- [Spring Security JWT 无状态认证](12-Spring-Security-JWT无状态认证.md)
- [JDBC URL](JDBC-URL.md)


