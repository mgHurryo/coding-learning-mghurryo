---
tags:
  - http
  - http-method
  - options
  - cors
---

# OPTIONS

## 定义

OPTIONS 方法用于获取目标资源支持的通信选项，例如服务器支持哪些 HTTP 方法、支持哪些自定义请求头。

## 主要特点

- **安全（Safe）**：不修改服务器状态。
- **幂等（Idempotent）**：多次相同的 OPTIONS 请求结果相同。
- **可缓存**：OPTIONS 响应可缓存，但通常缓存时间较短。
- **常用于 CORS 预检请求**。

## 使用场景

- 跨域请求前的预检（Preflight）。
- 查询服务器支持的方法列表。
- 检查服务器能力或可用特性。

## 示例

```http
OPTIONS /users/123 HTTP/1.1
Host: api.example.com
```

## 响应示例

```http
HTTP/1.1 204 No Content
Allow: GET, HEAD, PUT, DELETE, OPTIONS
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization
```

## CORS 预检

当浏览器发起复杂跨域请求（如自定义请求头、非简单方法）时，会先自动发送 OPTIONS 预检请求，确认服务器是否允许实际请求。详情见 [[CORS Preflight]]。

## 注意事项

- 服务器应正确返回 `Allow` 头，列出资源支持的方法。
- OPTIONS 请求不应包含请求体。

## 相关概念

- [[CORS Preflight]]
- [[HTTP Safety]]
- [[HTTP Idempotency]]
