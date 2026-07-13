---
title: Bearer Authentication
description: 使用 Authorization Bearer Token 进行 HTTP 认证的通用模式
tags:
  - Security
  - Authentication
  - Bearer
  - JWT
category: Security
---

# Bearer Authentication

Bearer Authentication 是一种 HTTP 认证方式，客户端在请求头中携带访问令牌：

```http
Authorization: Bearer <token>
```

“Bearer”的含义是：谁持有这个 Token，谁就可以用它访问受保护资源。因此 Token 泄露后风险很高。

## 常见流程

```text
登录或授权成功
  -> 服务端签发 access token
  -> 客户端保存 token
  -> 后续请求放入 Authorization 请求头
  -> 服务端验证 token
```

## 与 JWT 的关系

Bearer 说明 Token 如何放进 HTTP 请求；JWT 说明 Token 本身可以采用什么格式。

也就是说：

- Bearer 是传输约定。
- JWT 是令牌格式。

## Big-event 项目经验

Big-event 的 JWT 登录态适合使用 Bearer Token：

- 登录成功后返回 JWT。
- 后续请求使用 `Authorization: Bearer <token>`。
- Spring Security Filter 在 Controller 前解析和验证 Token。
- Token 无效或过期时返回 [401 Unauthorized](../../Network/HTTP/Status/HTTP-401-Unauthorized.md)。

## 相关主题

- [JWT 无状态认证](JWT-无状态认证.md)
- [JJWT 笔记](JJWT-笔记.md)
- [OAuth 2.0](OAuth-2.0.md)
- [Spring Security JWT 无状态认证](../../Java/Framework/Spring-Boot/Learning/Start/12-Spring-Security-JWT无状态认证.md)
- [401 Unauthorized](../../Network/HTTP/Status/HTTP-401-Unauthorized.md)

