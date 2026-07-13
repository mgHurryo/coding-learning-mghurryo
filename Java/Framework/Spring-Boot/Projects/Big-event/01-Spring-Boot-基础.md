---
title: Spring Boot 核心框架
description: 入口类、@SpringBootApplication、依赖注入、@Configuration 配置类与 application.yml 配置文件
created: 2026-06-28
tags: [spring-boot, java, 配置]
category: Spring-Boot
---

# 🟢 Spring Boot 核心框架

> 对应项目文件：`BigEventApplication.java`、`config/`、`application.yml`
> 关联笔记：[11-分层架构与-DTO-模式](11-分层架构与-DTO-模式.md) | [07-MySQL-数据库配置](07-MySQL-数据库配置.md)

---

## 一、Spring Boot 入口类

```java
// BigEventApplication.java
@MapperScan("top.hurrysite.mapper")
@SpringBootApplication
public class BigEventApplication {
    public static void main(String[] args) {
        SpringApplication.run(BigEventApplication.class, args);
    }
}
```

### @SpringBootApplication 组合注解

`@SpringBootApplication` 是以下三个注解的合成体：

| 注解 | 作用 |
|------|------|
| `@EnableAutoConfiguration` | 自动配置 Spring Boot（根据 classpath 依赖自动注入默认 Bean） |
| `@ComponentScan` | 扫描 `@Component`、`@Service`、`@Controller`、`@Configuration` 等注解 |
| `@Configuration` | 标记为 Java 配置类 |

> 💡 **原理：** `@EnableAutoConfiguration` 通过 `META-INF/spring.factories` 文件加载 100+ 自动配置类，仅当类路径中存在对应 jar 时才生效（如 `DataSourceAutoConfiguration`）。

### @MapperScan

```java
@MapperScan("top.hurrysite.mapper")
```

- 告诉 MyBatis 扫描 `top.hurrysite.mapper` 包下的所有 `@Mapper` 接口
- 等价于在每个 Mapper 接口上单独加 `@Mapper`
- 如果缺少此注解，MyBatis 无法创建 Mapper 代理对象

### SpringApplication.run()

```java
SpringApplication.run(BigEventApplication.class, args);
```

- 启动 Spring 应用上下文（IoC 容器）
- 执行自动配置
- 启动内嵌 Web 服务器（Tomcat）
- 返回 `ConfigurableApplicationContext`

---

## 二、依赖注入与控制反转

本项目使用 `@Autowired` 进行依赖注入：

```java
// UserServiceImpl.java
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserMapper userMapper;   // 字段注入
    
    @Autowired
    private JwtProperties jwtProperties;
}

// JwtFilter.java
@Component
public class JwtFilter extends OncePerRequestFilter {
    @Autowired
    private JwtProperties jwtProperties;
}
```

### DI 注入的三种方式

| 注入方式 | 本项目使用 | 推荐度 |
|---------|-----------|-------|
| **字段注入** (`@Autowired` 直接修饰字段) | ✅ 广泛使用 | ⚠️ 简单但不利于测试 |
| **构造器注入** (通过构造方法参数注入) | `SpringSecurityConfig` 中使用 | ✅ 推荐（不可变、可测试） |
| **Setter 注入** | 未使用 | 较少使用 |

> 💡 **构造器注入示例：**
> ```java
> @Configuration
> public class SpringSecurityConfig {
>     private final JwtFilter jwtFilter;
>     public SpringSecurityConfig(JwtFilter jwtFilter) {
>         this.jwtFilter = jwtFilter;
>     }
> }
> ```

### @Component 与 @Service 的区别

| 注解 | 用途 | 项目中用于 |
|------|------|-----------|
| `@Component` | 通用 Spring 组件 | `JwtFilter` |
| `@Service` | Service 层组件 | `UserServiceImpl` |

本质功能相同，语义上区分层次。

---

## 三、@Configuration 配置类

```java
// JwtConfig.java
@Configuration
@EnableConfigurationProperties(JwtProperties.class)
public class JwtConfig {
    // 空的配置类，仅用于触发 JwtProperties 的属性绑定
}

// SpringSecurityConfig.java
@Configuration
@EnableWebSecurity
public class SpringSecurityConfig {
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        // ...
    }
}
```

### @Configuration 注解

- 标记为 Java 配置类，替代传统的 `applicationContext.xml`
- 被 `@ComponentScan` 自动扫描并加载
- `@Bean` 方法会被代理，确保单例

### @Bean

```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    // 方法返回值注册为 Spring 容器中的 Bean
}
```

- 方法名是 Bean 的默认名称
- 方法参数自动注入（`HttpSecurity http`）
- 返回对象注册到 IoC 容器

### @EnableConfigurationProperties

```java
@Configuration
@EnableConfigurationProperties(JwtProperties.class)
```

显式启用 `@ConfigurationProperties` 的 Bean 注册。

---

## 四、@ConfigurationProperties 属性绑定

```java
// JwtProperties.java
@Data
@ConfigurationProperties(prefix = "jwt")
public class JwtProperties {
    private String secret;
    private long expire;
}
```

```yaml
# application.yml
jwt:
  secret: 1a2equ8UPDhdjskjhKJH9ihlkjbxbi0983247DUOCHLKlkjkhlk987
  expire: 3600000
```

### @ConfigurationProperties(prefix = "jwt")

- 自动绑定 `application.yml` 中以 `jwt.` 开头的配置项
- `jwt.secret` → `secret` 字段
- `jwt.expire` → `expire` 字段（自动类型转换 `String → long`）
- 无需手动读取配置文件

---

## 五、application.yml 配置文件

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/big_event?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: a987654321@

jwt:
  secret: 1a2equ8UPDhdjskjhKJH9ihlkjbxbi0983247DUOCHLKlkjkhlk987
  expire: 3600000

mybatis:
  configuration:
    map-underscore-to-camel-case: true
```

### YAML 语法要点

- 缩进表示层级（严格 2 空格）
- `key: value` 格式（冒号后必须有空格）
- `spring.datasource` 是 Spring Boot 预定义的配置前缀
- `jwt.*` 是自定义配置，绑定到 `JwtProperties`

---

## ★ 核心知识点总结

| 知识点 | 项目中的体现 | 源码位置 |
|-------|-------------|---------|
| `@SpringBootApplication` 组合注解 | 应用入口 | `BigEventApplication.java:13` |
| `@MapperScan` | 扫描 MyBatis Mapper | `BigEventApplication.java:12` |
| `@Autowired` 字段注入 | 注入 Mapper 和 Properties | `UserServiceImpl.java:19-22` |
| 构造器注入 | 注入 Filter | `SpringSecurityConfig.java:19-21` |
| `@Configuration` + `@Bean` | 配置 SecurityFilterChain | `SpringSecurityConfig.java:15-33` |
| `@ConfigurationProperties` | JWT 配置自动绑定 | `JwtProperties.java:9` |
| YAML 分层配置 | 数据库 + JWT + MyBatis | `application.yml` |

---

> 🔗 **下一步学习：** [02-RESTful-API设计](02-RESTful-API设计.md) → 了解 Controller 层如何接收 HTTP 请求
