---
title: HTTP 405 Method Not Allowed
description: 请求方法不被资源支持，服务器需返回 Allow 头告知支持的 HTTP 方法
tags:
  - http
  - status-code
  - 405-error-code
  - error
category: Network
---

# HTTP 405 Method Not Allowed

## 定义

`405 Method Not Allowed` 表示服务器知道请求的资源存在，但不支持该 HTTP 方法。

## 使用场景

- 对只读资源发送 POST。
- 对只支持 POST 的接口发送 GET。
- 资源方法权限配置错误。

## 示例

```http
DELETE /users/123 HTTP/1.1
Host: api.example.com
```

```http
HTTP/1.1 405 Method Not Allowed
Allow: GET, HEAD, PUT, PATCH
Content-Type: application/json

{
  "error": "Method Not Allowed",
  "message": "DELETE is not allowed on this resource"
}
```

## 关键响应头

- `Allow`：列出资源支持的 HTTP 方法。

## 注意事项

- 必须返回 `Allow` 头，告知客户端支持哪些方法。
- 405 与 404 的区别：资源存在，但方法不允许。

## 相关概念

- [OPTIONS](../Method/OPTIONS.md)
- [HTTP-404-Not-Found](HTTP-404-Not-Found.md)
- [RESTful-API-Design](../Concept/RESTful-API-Design.md)
