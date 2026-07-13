---
title: HTTP 503 Service Unavailable
description: 服务器暂时无法处理请求，常因过载或维护导致，可配合 Retry-After 头
tags:
  - http
  - status-code
  - 503-error-code
  - error
category: Network
---

# HTTP 503 Service Unavailable

## 定义

`503 Service Unavailable` 表示服务器暂时无法处理请求，通常是由于过载或维护。

## 使用场景

- 服务器负载过高。
- 服务正在维护或升级。
- 依赖服务不可用。

## 示例

```http
HTTP/1.1 503 Service Unavailable
Retry-After: 3600
Content-Type: application/json

{
  "error": "Service Unavailable",
  "message": "The server is temporarily unavailable, please try again later"
}
```

## 关键响应头

- `Retry-After`：告知客户端多久后重试。

## 与 500 的区别

| 状态码 | 含义 |
|--------|------|
| 500 | 服务器内部错误 |
| 503 | 服务暂时不可用，通常是可恢复的 |

## 相关概念

- [[HTTP-500-Internal-Server-Error]]
- [[HTTP-502-Bad-Gateway]]
- [[HTTP-429-Too-Many-Requests]]
