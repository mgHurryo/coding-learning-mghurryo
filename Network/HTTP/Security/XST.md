---
title: XST (Cross-Site Tracing)
description: 跨站追踪攻击原理，利用 TRACE 方法窃取 Cookie 的安全风险与防御措施
tags:
  - http
  - security
  - xst
  - cookie
category: Network
---

# XST

## 定义

XST（Cross-Site Tracing，跨站追踪）是一种利用 HTTP TRACE 方法读取浏览器中 HttpOnly Cookie 的攻击方式。

## 攻击原理

1. 攻击者诱使用户访问恶意页面。
2. 恶意页面通过 JavaScript 向目标网站发起 TRACE 请求。
3. 浏览器自动附带 Cookie，包括 HttpOnly Cookie。
4. 服务器通过 TRACE 方法将请求头回显给响应。
5. 恶意脚本读取响应中的 Cookie 信息。

## 为什么 TRACE 可以读取 HttpOnly Cookie

HttpOnly Cookie 原本无法被 JavaScript 读取，但 TRACE 方法会把请求头（包含 Cookie）回显在响应体中，攻击脚本从而可以获取。

## 防御措施

- **禁用 TRACE 方法**：大多数 Web 服务器默认禁用。
- 配置响应头 `X-Frame-Options`、`Content-Security-Policy` 等防止恶意页面嵌入。
- 使用现代浏览器安全策略。

## 相关概念

- [TRACE](../Method/TRACE.md)
- [HTTP-Safety](../Concept/HTTP-Safety.md)
- [HTTPS](../Concept/HTTPS.md)
