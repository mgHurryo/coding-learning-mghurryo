---
title: Spring Security JWT 无状态认证
description: Spring Boot 中使用 Spring Security 过滤器链集成 JWT 的通用模式
tags:
  - Spring-Boot
  - Spring-Security
  - JWT
  - Authentication
category: Spring-Boot
---

# 12. Spring Security JWT 无状态认证

Spring Security 负责认证、授权和常见攻击防护。Big-event 使用 Spring Security 配合 JWT 实现无状态认证，这是前后端分离后端 API 的常见模式。

## 1. 基本流程

```text
登录接口校验用户名密码
  -> 生成 JWT
  -> 前端保存 Token
  -> 后续请求携带 Authorization
  -> JwtFilter 解析 Token
  -> 认证通过后进入 Controller
```

## 2. SecurityFilterChain

常见配置项：

| 配置 | 作用 |
|------|------|
| 禁用 CSRF | JWT API 通常不依赖 Cookie Session |
| `SessionCreationPolicy.STATELESS` | 服务端不创建 Session |
| `permitAll()` | 放开登录、注册等白名单 |
| `authenticated()` | 其他请求必须认证 |
| `addFilterBefore()` | 插入自定义 JWT Filter |

## 3. JWT Filter 的职责

JWT Filter 适合做：

- 读取 `Authorization` 请求头。
- 判断是否存在 Bearer Token。
- 解析并验证 JWT。
- 把认证结果放入 SecurityContext。
- 认证失败时清理上下文或返回错误。

JWT Filter 不适合做：

- 注册用户。
- 查询业务详情。
- 编写复杂权限策略。
- 执行数据库写入。

## 4. Big-event 项目经验

- 登录和注册接口放入白名单。
- 其他接口默认需要认证。
- Token 解析异常交给全局异常处理。
- JWT 配置通过 `@ConfigurationProperties` 绑定，避免业务代码硬编码过期时间和密钥。

## 5. 与安全领域笔记的关系

本笔记关注 Spring Security 如何接入 JWT；JWT 本身的结构、Claims、签名算法和无状态认证取舍见：

- [[Security/Authentication/JWT 无状态认证]]
- [[Security/Authentication/Bearer Authentication\|Bearer Authentication]]
- [[Security/Authentication/JJWT 笔记]]

## 参考资料

- [Spring Security Reference](https://docs.spring.io/spring-security/reference/index.html)
- [JJWT GitHub](https://github.com/jwtk/jjwt)
