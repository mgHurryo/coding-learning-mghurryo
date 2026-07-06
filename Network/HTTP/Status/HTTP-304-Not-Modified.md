---
tags:
  - http
  - status-code
  - 304-error-code
  - caching
---

# HTTP 304 Not Modified

## 定义

`304 Not Modified` 表示客户端缓存的版本仍然有效，服务器不需要重新发送响应体。

## 使用场景

- 客户端发送条件请求（如 `If-None-Match` 或 `If-Modified-Since`）。
- 服务器判断资源未变化，返回 304，让客户端使用缓存副本。

## 示例

```http
GET /styles.css HTTP/1.1
Host: example.com
If-None-Match: "abc123"
```

```http
HTTP/1.1 304 Not Modified
ETag: "abc123"
Cache-Control: max-age=3600
```

## 与缓存的关系

304 是协商缓存（协商缓存）的结果，相比强缓存仍需要与服务器通信，但节省了传输响应体的带宽。

## 相关概念

- [[HTTP-Caching]]
- [[GET]]
- [[HTTP-200-OK]]
