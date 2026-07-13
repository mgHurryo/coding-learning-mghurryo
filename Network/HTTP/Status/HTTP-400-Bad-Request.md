---
title: HTTP 400 Bad Request
description: 客户端请求语法错误或参数无效，服务器无法处理
tags:
  - http
  - status-code
  - 400-error-code
  - error
category: Network
---

# HTTP 400 Bad Request

## 定义

`400 Bad Request` 表示服务器无法理解客户端的请求，通常是由于请求参数格式错误或缺少必要参数。

## 使用场景

- 请求体 JSON 格式错误。
- 缺少必填字段。
- 参数类型不匹配，如字符串传成了数字。
- URL 参数拼接错误。

## 示例

```http
POST /users HTTP/1.1
Content-Type: application/json

{
  "name": "Tom"
}
```

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "error": "Bad Request",
  "message": "email is required"
}
```

## 注意事项

- 400 表示客户端请求有问题，应提供具体错误信息帮助调试。
- 不要与 401、403、404 混淆。
- 区分 400（语法错误）和 422（语义错误，如 RFC 4918 定义）。

## 相关概念

- [HTTP-401-Unauthorized](HTTP-401-Unauthorized.md)
- [HTTP-403-Forbidden](HTTP-403-Forbidden.md)
- [HTTP-404-Not-Found](HTTP-404-Not-Found.md)
- [HTTP-422-Unprocessable-Entity](HTTP-422-Unprocessable-Entity.md)
