---
tags:
  - http
  - status-code
  - 401-error-code
  - auth
  - security
---

# HTTP 401 Unauthorized

## 定义

`401 Unauthorized` 表示请求需要用户身份认证，但客户端未提供或提供了无效的认证信息。

## 使用场景

- 访问需要登录的接口时未携带 Token。
- Token 过期或无效。
- 未提供 Basic Auth 凭据。

## 示例

```http
GET /orders HTTP/1.1
Host: api.example.com
```

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer
Content-Type: application/json

{
  "error": "Unauthorized",
  "message": "Token is missing or invalid"
}
```

## 关键响应头

- `WWW-Authenticate`：告知客户端应使用哪种认证方式。

## 401 vs 403

| 状态码 | 含义 | 场景 |
|--------|------|------|
| 401 | 未认证 | 不知道你是谁 |
| 403 | 禁止访问 | 知道你是谁，但你不允许访问 |

## 注意事项

- 401 强调认证失败。
- 403 强调授权失败（权限不足）。

## 相关概念

- [[HTTP 403 Forbidden]]
- [[HTTP 400 Bad Request]]
- [[HTTP 404 Not Found]]

## Big-event 项目经验

在 JWT 无状态认证中，`401 Unauthorized` 适合表达“请求没有通过认证”，例如：

- 没有携带 `Authorization` 请求头。
- Bearer Token 缺失或格式错误。
- Token 过期，需要重新登录。
- Token 签名校验失败。

相关实现见 [[Security/Authentication/JWT 无状态认证]] 和 [[Java/Framework/Spring-Boot/Learning/12-Spring-Security-JWT无状态认证]]。
