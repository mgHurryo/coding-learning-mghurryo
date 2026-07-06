---
tags:
  - http
  - http-method
  - post
  - rest
---

# POST

## 定义

POST 方法用于向服务器提交数据，通常用于创建新资源或触发某个处理动作。

## 主要特点

- **非安全（Not Safe）**：POST 请求会修改服务器状态。
- **非幂等（Not Idempotent）**：多次相同的 POST 请求通常会创建多个资源或产生多次副作用。
- **可缓存**：POST 响应默认不可缓存，但明确标记后可以被缓存。
- **参数传递**：数据放在请求体（request body）中，可支持多种编码格式。

## 常见 Content-Type

- `application/x-www-form-urlencoded`：表单默认编码。
- `multipart/form-data`：用于文件上传。
- `application/json`：API 接口最常用的格式。

## 使用场景

- 创建新用户、新订单、新文章等资源。
- 提交表单数据。
- 触发复杂查询或计算（如搜索条件过多不适合放 URL）。

## 示例

```http
POST /users HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "name": "Tom",
  "email": "tom@example.com"
}
```

## 注意事项

- 创建成功后通常返回 `201 Created`，响应头中包含新资源的 URL。
- 由于非幂等，网络重试可能导致重复创建，需配合幂等键等机制。

## Big-event 场景

Big-event 中注册和登录使用 POST：

- 注册会创建用户或提交注册动作。
- 登录会提交凭据并生成 JWT Token。
- 密码不应放在 URL 中，应通过请求体提交，并配合 HTTPS。

登录后的 Token 认证流程见 [[Security/Authentication/JWT-无状态认证]]。

## 相关概念

- [[HTTP-Idempotency]]
- [[RESTful-API-Design]]
- [[幂等接口设计]]
- [[HTTP-201-Created]]
- [[Security/Authentication/密码存储实践\|密码存储实践]]
- [[Java/Framework/Spring-Boot/Learning/10-RESTful-API与参数校验\|RESTful API 与参数校验]]
