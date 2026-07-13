---
title: HTTP 404 Not Found
description: 请求的资源不存在，服务器无法找到对应 URI 的资源
tags:
  - http
  - status-code
  - 404-error-code
  - error
category: Network
---

# HTTP 404 Not Found

## 定义

`404 Not Found` 表示服务器无法找到请求的资源。

## 使用场景

- 请求的资源 ID 不存在。
- URL 路径拼写错误。
- 资源已被删除。

## 示例

```http
GET /users/999999 HTTP/1.1
Host: api.example.com
```

```http
HTTP/1.1 404 Not Found
Content-Type: application/json

{
  "error": "Not Found",
  "message": "User with id 999999 does not exist"
}
```

## 注意事项

- 404 表示资源不存在，而不是服务器未运行。
- 有时为了安全，对无权限访问的资源也返回 404（隐藏资源存在性）。
- 不要滥用 404 处理所有错误。

## 相关概念

- [HTTP-400-Bad-Request](HTTP-400-Bad-Request.md)
- [HTTP-401-Unauthorized](HTTP-401-Unauthorized.md)
- [HTTP-403-Forbidden](HTTP-403-Forbidden.md)
- [HTTP-405-Method-Not-Allowed](HTTP-405-Method-Not-Allowed.md)
