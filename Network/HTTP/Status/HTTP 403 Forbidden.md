---
tags:
  - http
  - status-code
  - 403-error-code
  - auth
  - security
---

# HTTP 403 Forbidden

## 定义

`403 Forbidden` 表示服务器理解请求，但拒绝执行，因为客户端没有访问该资源的权限。

## 使用场景

- 已登录用户尝试访问无权限的资源。
- IP 被黑名单限制。
- 需要特定角色或权限才能访问的接口。

## 示例

```http
GET /admin/users HTTP/1.1
Authorization: Bearer user-token
```

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "error": "Forbidden",
  "message": "You do not have permission to access this resource"
}
```

## 401 vs 403

| 状态码 | 含义 | 场景 |
|--------|------|------|
| 401 | 未认证 | 没有登录或 Token 无效 |
| 403 | 禁止访问 | 已登录但权限不足 |

## 注意事项

- 403 不需要客户端重新认证，因为问题不在认证而在授权。
- 如果资源存在但不想暴露，也可以返回 403 或 404（模糊处理）。

## 相关概念

- [[HTTP 401 Unauthorized]]
- [[HTTP 404 Not Found]]
- [[HTTP 400 Bad Request]]
