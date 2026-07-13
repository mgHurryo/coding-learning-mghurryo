---
title: TRACE
description: HTTP TRACE 方法，用于诊断请求路径，返回请求在服务器端的镜像
tags:
  - http
  - http-method
  - trace
  - security
category: Network
---

# TRACE

## 定义

TRACE 方法用于将服务器收到的请求回显给客户端，主要用于诊断和调试。

## 主要特点

- **安全（Safe）**：不修改服务器状态。
- **幂等（Idempotent）**：多次相同请求结果相同。
- **主要用于诊断**。
- **存在安全风险**。

## 使用场景

- 诊断请求在代理、网关、负载均衡器之间的变化情况。
- 调试 HTTP 请求头是否被中间设备修改。

## 示例

```http
TRACE /users/123 HTTP/1.1
Host: api.example.com
```

## 安全风险

TRACE 方法可能导致 [XST](../Security/XST.md)（Cross-Site Tracing）攻击：攻击者利用 TRACE 读取浏览器中的 HttpOnly Cookie。因此，生产环境中通常应禁用 TRACE 方法。

## 注意事项

- 大多数 Web 服务器（如 Nginx、Apache）默认禁用 TRACE。
- 除非必要，否则不要在生产环境启用。

## 相关概念

- [HTTP-Safety](../Concept/HTTP-Safety.md)
- [HTTP-Idempotency](../Concept/HTTP-Idempotency.md)
- [XST](../Security/XST.md)
