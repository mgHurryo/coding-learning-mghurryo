---
title: Security MOC
description: 安全与认证知识索引，涵盖 JWT、OAuth2、CORS、传输安全
tags:
  - Security
  - MOC
  - 索引
category: Security
---

# Security MOC

> 网络安全与身份认证知识的索引地图。

## 认证与授权

| 笔记 | 说明 |
| :--- | :--- |
| [JWT 无状态认证](Authentication/JWT-无状态认证.md) | JWT 结构、Bearer Token 流程与无状态认证取舍 |
| [Bearer Authentication](Authentication/Bearer-Authentication.md) | Authorization 请求头携带 Token 的认证方式 |
| [JJWT 笔记](Authentication/JJWT-笔记.md) | Java JWT 库的使用：创建、解析、签名 |
| [OAuth 2.0](Authentication/OAuth-2.0.md) | 授权框架概览 |
| [密码存储实践](Authentication/密码存储实践.md) | MD5 风险、BCrypt 与 PasswordEncoder |
| [401 Unauthorized](../Network/HTTP/Status/HTTP-401-Unauthorized.md) | HTTP 未认证状态码 |

## 传输安全

| 笔记 | 说明 |
| :--- | :--- |
| [HTTPS](../Network/HTTP/Concept/HTTPS.md) | 加密传输 |
| [TLS 握手](../Network/HTTP/Concept/TLS-Handshake.md) | 加密连接建立流程 |

## 相关主题

- [网络 MOC](../Network/MOC.md)
- [HTTP MOC](../Network/HTTP/MOC.md)
- [Spring Boot 安全整合](../Java/Framework/Spring-Boot/MOC.md)
- [Spring Security JWT 无状态认证](../Java/Framework/Spring-Boot/Learning/Start/12-Spring-Security-JWT无状态认证.md)
