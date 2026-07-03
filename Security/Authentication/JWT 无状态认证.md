---
title: JWT 无状态认证
description: JWT 的结构、Bearer Token 认证流程、无状态认证取舍和项目实践
tags:
  - JWT
  - Authentication
  - Security
  - Stateless
category: Security
---

# JWT 无状态认证

JWT（JSON Web Token）是一种自包含 Token，常用于前后端分离系统中的无状态认证。

## 1. JWT 结构

JWT 通常由三段组成：

```text
header.payload.signature
```

| 部分 | 作用 |
|------|------|
| Header | Token 类型和签名算法 |
| Payload | Claims，例如用户标识、签发时间、过期时间 |
| Signature | 使用密钥签名，防止 Token 被篡改 |

注意：Payload 默认是编码，不是加密，不要放密码、身份证号、银行卡号等敏感信息。

## 2. Bearer Token 流程

常见流程：

```text
用户登录
  -> 服务端校验账号密码
  -> 生成 JWT
  -> 客户端后续请求携带 Authorization: Bearer <token>
  -> 服务端验证签名和过期时间
  -> 认证通过后执行接口
```

## 3. 无状态认证

无状态认证意味着服务端不保存 Session。每个请求必须携带完整认证信息。

优点：

- 更适合前后端分离。
- 服务端横向扩展更容易。
- 不依赖单机 Session。

代价：

- Token 泄露后，在过期前可能被滥用。
- 主动登出、踢人下线、权限实时变更需要额外设计。
- 需要合理设置过期时间和刷新策略。

## 4. Big-event 项目经验

Big-event 使用 JWT 表示登录态：

- 登录成功后返回 Token。
- 需要认证的接口从请求头读取 Token。
- Spring Security Filter 在 Controller 之前校验 Token。
- Token 过期、格式错误、签名错误应返回稳定的错误响应。

对应 Spring Boot 实现见 [[Java/Framework/Spring-Boot/Learning/12-Spring-Security-JWT无状态认证]]。

## 5. 实战注意点

- 密钥放配置或密钥管理系统，不写死在代码中。
- Token 过期时间不宜过长。
- Payload 中只放必要身份信息。
- 生产项目可考虑 access token + refresh token。
- 对高风险系统，可配合 Redis 黑名单实现主动失效。

## 相关主题

- [[Security/Authentication/JJWT 笔记]]
- [[Security/Authentication/Bearer Authentication\|Bearer Authentication]]
- [[Network/HTTP/Status/HTTP 401 Unauthorized]]
- [[Network/HTTP/Method/POST]]
- [[Network/HTTP/Concept/RESTful API Design]]
- [[Java/Framework/Spring-Boot/Learning/12-Spring-Security-JWT无状态认证]]

## 参考资料

- [JJWT GitHub](https://github.com/jwtk/jjwt)
- [Spring Security Reference](https://docs.spring.io/spring-security/reference/index.html)
