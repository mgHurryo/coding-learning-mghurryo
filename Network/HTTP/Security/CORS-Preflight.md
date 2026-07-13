---
title: CORS Preflight
description: 跨域资源共享的预检请求机制，OPTIONS 方法与 CORS 头部交互流程
tags:
  - http
  - cors
  - security
  - options
category: Network
---

# CORS Preflight

## 定义

CORS 预检请求（Preflight Request）是浏览器在发送复杂跨域请求之前，自动发起的一个 [[OPTIONS]] 请求，用于确认服务器是否允许实际请求。

## 什么情况下会触发预检

以下情况浏览器会自动发送预检请求：

- 使用 PUT、PATCH、DELETE 等非简单方法。
- 使用 `Content-Type: application/json` 等非简单内容类型。
- 请求中包含自定义请求头，如 `Authorization`、`X-Requested-With`。

## 简单请求 vs 复杂请求

| 特征 | 简单请求 | 复杂请求 |
|------|----------|----------|
| 方法 | GET、HEAD、POST | PUT、PATCH、DELETE 等 |
| Content-Type | `application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain` | 其他类型如 `application/json` |
| 自定义头 | 无 | 有 |
| 预检 | 不需要 | 需要 |

## 预检请求示例

```http
OPTIONS /users/123 HTTP/1.1
Host: api.example.com
Origin: https://example.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: Content-Type, Authorization
```

## 预检响应示例

```http
HTTP/1.1 204 No Content
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Max-Age: 86400
```

## 关键响应头

- `Access-Control-Allow-Origin`：允许的源。
- `Access-Control-Allow-Methods`：允许的方法。
- `Access-Control-Allow-Headers`：允许的请求头。
- `Access-Control-Allow-Credentials`：是否允许携带凭证（Cookie）。
- `Access-Control-Max-Age`：预检结果缓存时间。

## 注意事项

- 预检请求由浏览器自动发起，开发者无需手动发送。
- 服务器必须正确响应 OPTIONS 请求，否则实际请求会被浏览器拦截。
- `Access-Control-Allow-Origin` 不能同时设置为 `*` 并允许 credentials。

## 相关概念

- [[OPTIONS]]
- [[HTTP-204-No-Content]]
- [[HTTP-Safety]]
