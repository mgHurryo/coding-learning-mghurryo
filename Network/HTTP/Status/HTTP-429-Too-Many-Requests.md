---
title: HTTP 429 Too Many Requests
description: 请求过多时的限流状态码，与 Retry-After 和接口保护相关
tags:
  - http
  - status-code
  - 429-error-code
  - rate-limit
category: Network
---

# HTTP 429 Too Many Requests

## 定义

`429 Too Many Requests` 表示客户端在给定时间内发送了太多请求，触发了服务端限流策略。

## 使用场景

- 登录接口连续失败或请求过快。
- 短时间内频繁调用高成本 API。
- 单个用户、IP、Token 或租户超过访问配额。

## 示例

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 60
Content-Type: application/json

{
  "error": "Too Many Requests",
  "message": "Please retry after 60 seconds"
}
```

## 与 Big-event 的连接

Big-event 当前重点是 JWT 登录态和基础接口保护。后续如果扩展“密码错误次数限制”“登录防暴力破解”“接口访问频率限制”，就可以使用 429 表达限流结果。

实现位置通常不在 Controller 内部，而是在 Filter、Interceptor、网关或专门的限流组件中。

## 相关概念

- [401 Unauthorized](HTTP-401-Unauthorized.md)
- [503 Service Unavailable](HTTP-503-Service-Unavailable.md)
- [密码存储实践](../../../Security/Authentication/密码存储实践.md)
- [Spring Security JWT 无状态认证](../../../Java/Framework/Spring-Boot/Learning/Start/12-Spring-Security-JWT无状态认证.md)

