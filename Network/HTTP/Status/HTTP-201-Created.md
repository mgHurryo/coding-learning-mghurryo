---
title: HTTP 201 Created
description: 资源创建成功的响应，常用于 POST 请求创建新资源后的返回
tags:
  - http
  - status-code
  - 201-error-code
  - post
category: Network
---

# HTTP 201 Created

## 定义

`201 Created` 表示请求已成功处理，并在服务器上创建了新资源。

## 使用场景

- [[POST]] 请求成功创建资源。
- 某些 [[PUT]] 请求在资源不存在时创建资源。

## 示例

```http
HTTP/1.1 201 Created
Location: /users/123
Content-Type: application/json

{
  "id": 123,
  "name": "Tom",
  "email": "tom@example.com"
}
```

## 关键响应头

- `Location`：指向新创建资源的 URL。

## 注意事项

- 通常与 POST 方法配合使用。
- 应在响应体中返回新创建资源的完整或部分表示。
- `Location` 头是可选但推荐的。

## 相关概念

- [[POST]]
- [[PUT]]
- [[HTTP-200-OK]]
- [[RESTful-API-Design]]
