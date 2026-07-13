---
title: RESTful API 与参数校验
description: Spring Boot 中 REST Controller、参数绑定、统一响应和 Bean Validation 实践
tags:
  - Spring-Boot
  - Spring-MVC
  - REST
  - Validation
category: Spring-Boot
---

# 10. RESTful API 与参数校验

Spring Boot Web 项目通常使用 Spring MVC 编写 REST API。Big-event 中的用户注册、登录、查询、修改信息接口，集中体现了 Controller、DTO、参数绑定和 Bean Validation 的组合用法。

## 1. @RestController

`@RestController` 等价于 `@Controller` 加 `@ResponseBody`，表示方法返回值会直接序列化为 JSON。

适合场景：

- 前后端分离接口。
- 移动端 API。
- 后端服务之间的 JSON 接口。

## 2. HTTP 方法选择

| 操作意图 | 推荐方法 | Big-event 经验 |
|----------|----------|----------------|
| 查询信息 | GET | 查询用户信息 |
| 提交动作 | POST | 注册、登录 |
| 部分修改 | PATCH | 修改用户信息、修改密码 |

HTTP 方法语义可继续看：

- [[Network/HTTP/Guide/HTTP-Method-选择指南]]
- [[Network/HTTP/Concept/RESTful-API-Design]]

## 3. 参数绑定

| 参数来源 | Spring MVC 写法 | 适合场景 |
|----------|-----------------|----------|
| 查询参数 | 普通对象参数或 `@RequestParam` | 查询、筛选 |
| JSON 请求体 | `@RequestBody` | 复杂对象提交 |
| 请求头 | `@RequestHeader` | Token、客户端信息 |
| 路径变量 | `@PathVariable` | `/users/{id}` |

Big-event 中，注册和登录使用表单/普通对象绑定，更新信息和修改密码使用 `@RequestBody` 接收 JSON。

## 4. DTO 承接外部输入

DTO 适合做接口入参模型：

- 字段只保留当前接口需要的数据。
- 校验注解直接写在 DTO 字段上。
- 避免把数据库实体直接暴露给外部请求。

相关架构说明见 [[Java/Framework/Spring-的一般项目结构]]。

## 5. Bean Validation

常见校验注解：

| 注解 | 作用 |
|------|------|
| `@Valid` | 触发对象字段校验 |
| `@Validated` | Spring 提供的校验入口，支持分组校验 |
| `@Pattern` | 正则校验 |
| `@NotBlank` | 字符串非空、非空白 |
| `@Email` | 邮箱格式校验 |
| `@AssertTrue` | 方法级布尔校验，适合跨字段规则 |

Big-event 中的“确认新密码一致”适合用 `@AssertTrue` 做跨字段校验。

## 6. 统一响应

常见响应结构：

```text
code + message + data
```

优点：

- 前端处理成功和失败更统一。
- Controller 返回值更稳定。
- 可以和全局异常处理配合。

更完整的错误响应设计见 [[11-全局异常处理与统一响应]]。

## 参考资料

- [Spring Framework Web MVC Reference](https://docs.spring.io/spring-framework/reference/web.html)
- [Jakarta Bean Validation](https://jakarta.ee/specifications/bean-validation/3.0/)
- [Baeldung: Spring RequestBody and ResponseBody](https://www.baeldung.com/spring-request-response-body)

