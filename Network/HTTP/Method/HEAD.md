---
tags:
  - http
  - http-method
  - head
  - caching
---

# HEAD

## 定义

HEAD 方法与 GET 类似，但服务器只返回响应头，不返回响应体。

## 主要特点

- **安全（Safe）**：不修改服务器状态。
- **幂等（Idempotent）**：多次相同的 HEAD 请求结果相同。
- **可缓存**：HEAD 响应可缓存，缓存语义与 GET 一致。
- **无响应体**：仅返回与 GET 请求相同的响应头。

## 使用场景

- 检查资源是否存在。
- 获取资源的元信息，如文件大小（`Content-Length`）、最后修改时间（`Last-Modified`）、类型（`Content-Type`）。
- 验证缓存是否仍然有效。

## 示例

```http
HEAD /users/123 HTTP/1.1
Host: api.example.com
```

## 响应示例

```http
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 1024
Last-Modified: Wed, 21 Oct 2025 07:28:00 GMT
```

## 注意事项

- HEAD 响应头应与对应 GET 请求完全一致。
- 适合在下载大文件前预估资源信息。

## 相关概念

- [[GET]]
- [[HTTP Caching]]
- [[HTTP 200 OK]]
