---
tags:
  - http
  - http-method
  - get
  - rest
---

# GET

## 定义

GET 方法用于请求指定的资源。它是 HTTP 协议中最常用的方法之一，主要用于从服务器获取数据。

## 主要特点

- **安全性（Safe）**：GET 请求不应修改服务器状态。
- **幂等性（Idempotent）**：多次相同的 GET 请求应返回相同结果（假设资源未被其他请求修改）。
- **可缓存（Cacheable）**：GET 响应通常可以被浏览器或代理缓存。
- **参数传递**：参数通过 URL 的查询字符串（query string）传递，例如 `?id=1&name=tom`。

## 使用场景

- 获取页面、图片、CSS、JS 等静态资源。
- 查询数据库记录。
- 搜索、过滤、分页等只读操作。

## 示例

```http
GET /users/123 HTTP/1.1
Host: api.example.com
```

## 注意事项

- URL 长度有限制（浏览器通常限制在 2KB~8KB）。
- 避免在 GET 请求中传递敏感信息，因为参数会暴露在 URL 和浏览器历史中。
- GET 请求不应产生副作用，如创建、更新或删除数据。

## 相关概念

- [[HTTP Safety]]
- [[HTTP Idempotency]]
- [[HTTP Caching]]
- [[RESTful API Design]]
