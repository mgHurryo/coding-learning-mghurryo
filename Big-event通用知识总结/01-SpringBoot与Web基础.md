---
created: 2026-07-03
tags: [spring-boot, spring-mvc, rest-api, validation]
source: Java/Framework/Spring-Boot/Projects/Big-event
---

# Spring Boot 与 Web 基础

## 1. Spring Boot 应用启动

`@SpringBootApplication` 是 Spring Boot 项目的入口核心，它组合了三个常用能力：

| 注解 | 通用作用 |
|------|----------|
| `@SpringBootConfiguration` / `@Configuration` | 声明当前类是配置类 |
| `@EnableAutoConfiguration` | 根据依赖自动装配常见组件 |
| `@ComponentScan` | 扫描当前包及子包下的 Spring 组件 |

通用理解：

- Spring Boot 项目启动时，会创建 IoC 容器。
- 被 `@Controller`、`@Service`、`@Component`、`@Configuration` 等注解标记的类会被容器管理。
- 业务代码不是自己 `new` 对象，而是交给 Spring 创建、注入和管理。

## 2. 依赖注入

依赖注入用于解决对象之间的协作关系。

| 注入方式 | 特点 | 建议 |
|----------|------|------|
| 字段注入 | 写法简单，但不利于测试和不可变设计 | 学习阶段可用 |
| 构造器注入 | 依赖明确、便于测试、支持 `final` 字段 | 项目中更推荐 |
| Setter 注入 | 适合可选依赖 | 较少作为默认选择 |

通用经验：

- Controller 注入 Service。
- Service 注入 Mapper、工具配置、其他业务组件。
- 配置类可以通过构造器注入 Filter、Provider 等组件。

## 3. 配置类与 Bean

`@Configuration` 表示一个 Java 配置类，`@Bean` 方法的返回值会注册进 Spring 容器。

常见场景：

- 注册安全过滤链。
- 注册密码加密器。
- 注册跨域配置。
- 注册第三方 SDK 客户端。

核心理解：

```java
@Bean
public SomeType someBean() {
    return new SomeType();
}
```

这段代码的含义不是普通方法调用，而是告诉 Spring：以后容器里有一个 `SomeType` 类型的 Bean。

## 4. 配置文件与属性绑定

Spring Boot 常用 `application.yml` 管理配置。

通用配置类型：

- `spring.datasource.*`：数据库连接。
- `mybatis.configuration.*`：MyBatis 行为。
- 自定义前缀：例如 `jwt.secret`、`jwt.expire`。

`@ConfigurationProperties` 可以把配置文件中的一组配置绑定到 Java 对象中：

```java
@ConfigurationProperties(prefix = "jwt")
public class JwtProperties {
    private String secret;
    private long expire;
}
```

通用价值：

- 避免把密钥、过期时间、地址等配置硬编码在业务代码里。
- 多环境部署时可以换配置，不需要改代码。

## 5. RESTful API 设计

`@RestController` 等价于 `@Controller` 加 `@ResponseBody`，适合直接返回 JSON。

常用映射注解：

| 注解 | HTTP 方法 | 常见语义 |
|------|-----------|----------|
| `@GetMapping` | GET | 查询资源 |
| `@PostMapping` | POST | 新增、登录、提交动作 |
| `@PutMapping` | PUT | 全量更新 |
| `@PatchMapping` | PATCH | 局部更新 |
| `@DeleteMapping` | DELETE | 删除资源 |

通用原则：

- 查询数据优先用 GET。
- 创建资源优先用 POST。
- 修改部分字段优先用 PATCH。
- URL 描述资源，HTTP 方法描述动作。

## 6. 请求参数绑定

Spring MVC 可以从不同位置读取参数。

| 数据位置 | 常用方式 | 适合场景 |
|----------|----------|----------|
| URL 查询参数 | 普通对象参数或 `@RequestParam` | 查询、筛选、分页 |
| JSON 请求体 | `@RequestBody` | 复杂对象提交 |
| 请求头 | `@RequestHeader` | Token、客户端信息 |
| 路径变量 | `@PathVariable` | `/users/{id}` 这类资源路径 |
| 表单参数 | 普通对象参数 | 表单登录、简单提交 |

通用注意点：

- JSON 请求体需要 `@RequestBody`。
- GET 请求通常不要用请求体。
- DTO 字段名要和请求参数名保持一致，才能自动绑定。

## 7. 统一响应对象

统一响应对象通常包含：

| 字段 | 作用 |
|------|------|
| `code` | 表示业务成功或失败 |
| `message` | 给前端或用户看的提示信息 |
| `data` | 真正返回的数据 |

通用价值：

- 前端处理接口结果更稳定。
- 成功、失败、异常都可以保持同一结构。
- 泛型 `Result<T>` 可以适配不同类型的返回数据。

常见写法：

```java
Result.success(data);
Result.success();
Result.error("操作失败");
```

## 8. Bean Validation 参数校验

Bean Validation 用来在 Controller 入参阶段拦截非法数据。

常见注解：

| 注解 | 用途 |
|------|------|
| `@Valid` | 触发对象字段校验 |
| `@Validated` | Spring 提供的校验入口，支持分组校验 |
| `@Pattern` | 正则表达式校验 |
| `@NotBlank` | 字符串不能为 null、空串、纯空格 |
| `@Email` | 邮箱格式校验 |
| `@AssertTrue` | 方法级布尔校验，适合跨字段判断 |

通用经验：

- DTO 负责接收和校验外部输入。
- Controller 方法参数上加 `@Valid`。
- `@AssertTrue` 适合“确认密码和新密码一致”这类跨字段校验。
- null 值和格式错误最好分开处理，错误提示更清晰。

