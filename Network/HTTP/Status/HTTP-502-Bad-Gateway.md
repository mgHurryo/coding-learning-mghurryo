---
title: HTTP 502 Bad Gateway
description: 网关或代理收到上游服务器的无效响应，常见于反向代理场景
tags:
  - http
  - status-code
  - 502-error-code
  - error
category: Network
---

# HTTP 502 Bad Gateway

## 定义

`502 Bad Gateway` 表示网关或代理服务器从上游服务器接收到无效响应。

## 使用场景

- 反向代理无法连接到后端服务。
- 后端服务异常崩溃或返回错误格式响应。
- 上游服务器超时或无响应。

## 示例

```http
HTTP/1.1 502 Bad Gateway
Content-Type: application/json

{
  "error": "Bad Gateway",
  "message": "The upstream server returned an invalid response"
}
```

## 常见原因

- 后端服务未启动或端口配置错误。
- 后端返回的响应头过大或格式错误。
- 代理与后端网络不通。

## 相关概念

- [HTTP-500-Internal-Server-Error](HTTP-500-Internal-Server-Error.md)
- [HTTP-503-Service-Unavailable](HTTP-503-Service-Unavailable.md)
- [HTTP-Proxy](../Concept/HTTP-Proxy.md)
