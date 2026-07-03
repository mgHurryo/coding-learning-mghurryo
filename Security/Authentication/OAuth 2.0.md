---
title: OAuth 2.0
description: OAuth 2.0 授权框架的核心概念与四种授权模式
tags:
  - oauth
  - auth
  - security
  - authorization
category: Security
---

# OAuth 2.0

> OAuth 2.0 是一种行业标准的授权协议，允许第三方应用在用户授权的前提下访问用户在服务商上的受保护资源，而无需暴露用户的凭证。

## 核心角色

| 角色 | 说明 | 示例 |
| :--- | :--- | :--- |
| **资源所有者**（Resource Owner） | 拥有受保护资源的用户 | 你本人 |
| **客户端**（Client） | 请求访问资源的第三方应用 | 一个需要读取你 Google 日历的 App |
| **授权服务器**（Authorization Server） | 验证身份并颁发 token | Google 认证中心 |
| **资源服务器**（Resource Server） | 存储受保护资源并验证 token | Google 日历 API |

## 四种授权模式

### 1. 授权码模式（Authorization Code）

最常用、最安全的方式，适合有后端的 Web 应用。

```
用户 → 客户端 → 授权服务器（登录并授权）→ 授权码 → 客户端 → 授权服务器（换 token）→ 访问令牌
```

### 2. 隐式模式（Implicit）

适用于纯前端应用（已不推荐，改为 PKCE 替代）。

### 3. 密码模式（Resource Owner Password Credentials）

用户直接向客户端提供用户名密码，仅限受信任的应用。**已不推荐使用**。

### 4. 客户端凭证模式（Client Credentials）

适用于服务间通信，无需用户交互。

## 访问令牌（Access Token）

通常使用 [[Security/Authentication/JJWT 笔记\|JWT]] 格式，包含用户标识、权限范围、过期时间等信息。

## 相关概念

- [[Security/Authentication/JJWT 笔记\|JJWT 笔记]]
- [[Network/HTTP/Status/HTTP 401 Unauthorized\|401 Unauthorized]]
- [[Security/Authentication/Bearer Authentication\|Bearer Authentication]]
