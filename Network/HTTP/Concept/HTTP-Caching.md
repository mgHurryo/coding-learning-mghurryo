---
title: HTTP Caching
description: HTTP 缓存机制，包括强缓存、协商缓存与缓存控制头
tags:
  - http
  - concept
  - caching
  - performance
category: Network
---

# HTTP Caching

## 定义

HTTP 缓存是指客户端或中间代理存储 HTTP 响应副本，以便在后续相同请求中直接使用，从而减少网络延迟和服务器负载。

## 缓存位置

- **浏览器缓存**：存储在用户本地浏览器中。
- **代理缓存**：位于客户端和服务器之间的代理服务器上。
- **CDN 缓存**：内容分发网络节点缓存。
- **反向代理缓存**：如 Nginx、Varnish 等。

## 与 HTTP 方法的缓存关系

| 方法 | 是否默认可缓存 | 说明 |
|------|----------------|------|
| [[GET]] | ✅ | 最常见的可缓存方法 |
| [[HEAD]] | ✅ | 缓存语义与 GET 一致 |
| [[POST]] | ❌ | 默认不可缓存，但可显式标记 |
| [[PUT]] | ❌ | 通常不可缓存 |
| [[PATCH]] | ❌ | 通常不可缓存 |
| [[DELETE]] | ❌ | 不可缓存 |

## 主要缓存头

- `Cache-Control`：最核心、最灵活的缓存控制头。
  - `max-age=3600`：缓存有效期 3600 秒。
  - `no-cache`：使用前必须重新验证。
  - `no-store`：完全禁止缓存。
  - `public`：可被任何缓存存储。
  - `private`：仅浏览器可缓存。
- `Expires`：指定缓存过期时间（绝对时间）。
- `ETag`：资源的实体标签，用于条件请求验证。
- `Last-Modified`：资源最后修改时间。
- `If-None-Match` / `If-Modified-Since`：条件请求头。

## 缓存策略

- **强缓存**：直接使用本地缓存，不向服务器发请求。
- **协商缓存**：向服务器验证资源是否变化，未变化返回 `304 Not Modified`。

## 示例

```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: public, max-age=3600
ETag: "abc123"
```

## 注意事项

- 缓存策略应根据资源特性选择，静态资源可长期缓存，动态数据需谨慎。
- 涉及用户隐私或敏感信息的响应应使用 `private` 或 `no-store`。

## 相关概念

- [[GET]]
- [[HEAD]]
- [[HTTP-200-OK]]
- [[HTTP-304-Not-Modified]]
