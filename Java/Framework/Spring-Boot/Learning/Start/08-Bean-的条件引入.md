---
title: Bean 的条件引入
description: Spring Boot 中 @Conditional 系列注解控制 Bean 注册的条件
tags:
  - Spring
  - Spring-Boot
  - Bean
  - Conditional
  - 条件注解
category: Spring-Boot
---

# 08. Bean 的条件引入

[上一章：Bean 对象的注册](07-Bean-对象的注册.md) | [下一章：Spring-Boot 自动配置的原理](09-Spring-Boot自动配置的原理.md)

Spring 中提供了的 `@Conditional` 条件注解来控制 Bean 的注册, 当条件满足时, Bean 才会被 Spring 注册

常用的 `@Conditional` 条件:

|            注解            |               说明               |
| :----------------------: | :----------------------------: |
| `@ConditionalOnProperty` |    配置文件中存在对应的属性, 才声明该 Bean     |
| `@ConditionalOnMissBean` | 当环境中不存在当前类型的 Bean 时, 才声明该 Bean |
|  `@ConditionalOnClass`   |    当环境中存在指定的这个类时, 才声明该 Bean    |

## `@ConditionalOnProperty(prefix="", name="", havingValue="", matchIfMissing="")`

1. prefix: 配置项的前缀, 例如 country.name, 前缀为 country
2. name: 配置项的具体名字, 例如 country.name, 名字为 name, 当然可以不写 prefix, 直接写  country.name
3. havingValue: 配置项的值, 例如在配置文件中 country.name="china", 且 havingValue 上填写的也是 "china", 则创建 Bean, 反之
4. matchIfMissing: 默认为 false, 如果为 true, 则当配置文件中不存在指定的配置项, 则默认创建 Bean

```Java
@Configuration
public class countryConfig {
	@Bean
	@ConditionalOnProperty(prefix="country", name={"name", "code"})
	public Country country() {
		System.out.println("country.name: " + name);  
		System.out.println("country.code: " + code);  
		return new Country(name, code);
	}
}
```

配置文件:

```Java
country:
	name: US
	code: US
```

此时, Spring 就会创建 Country 这个 Bean 类. 当配置文件中的 country 的 name 以及 code 字段不存在时, 则不创建 Country 的 Bean.

## `@ConditionalOnMissBean()`

1. value: 按照类型判断, 如果没有这个类型, 则创建
	- `@ConditionalOnMissingBean(UserService.class)`
2. name: 按照 Bean 的名字判断, 如果没有这个 Bean 的名字, 则创建
	- `@ConditionalOnMissingBean(name = "userService")`
3. type: 按照类的完整字符串来判断, 如果容器中没有这个类型的 Bean, 则创建
	- `@ConditionalOnMissingBean(type = "com.example.UserService")`
4. annotation: 如果容器中没有一个 Bean 对象被指定的标签标记, 则创建
	- `@ConditionalOnMissingBean(annotation = MyMarker.class)`
5. 啥字段都不写, 判断自身 Bean 对象是否存在与容器中，如果不存在则创建.

```Java
@Configuration
public class UserConfig {
	@Bean
	@ConditionalOnMissBean(value="UserServices")
	public UserServices userServices() {
		return new UserServices;
	}
}
```

## `@ConditionalOnClass()`

1. value: 如果当前项目中存在了 value 这个类, 则创建
	- `@ConditionalOnClass(UserServices.class)`
2. name: 如果当前项目中存在了 name 这个类, 则创建
	- `@ConditionalOnClass("top.hurrysite.Services.UserServices")`

```Java
@Configuration
public class UserConfig {
	@Bean
	@ConditionalOnClass(value="UserServices.class")
	public UserSserevices userServices() {
		return new UserServices();
	}
}
```
