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
| [[Security/Authentication/JWT 无状态认证\|JWT 无状态认证]] | JWT 结构、Bearer Token 流程与无状态认证取舍 |
| [[Security/Authentication/Bearer Authentication\|Bearer Authentication]] | Authorization 请求头携带 Token 的认证方式 |
| [[Security/Authentication/JJWT 笔记\|JJWT 笔记]] | Java JWT 库的使用：创建、解析、签名 |
| [[Security/Authentication/OAuth 2.0\|OAuth 2.0]] | 授权框架概览 |
| [[Security/Authentication/密码存储实践\|密码存储实践]] | MD5 风险、BCrypt 与 PasswordEncoder |
| [[Network/HTTP/Status/HTTP 401 Unauthorized\|401 Unauthorized]] | HTTP 未认证状态码 |

## 传输安全

| 笔记 | 说明 |
| :--- | :--- |
| [[Network/HTTP/Concept/HTTPS\|HTTPS]] | 加密传输 |
| [[Network/HTTP/Concept/TLS Handshake\|TLS 握手]] | 加密连接建立流程 |

## 相关主题

- [[Network/MOC\|网络 MOC]]
- [[Network/HTTP/MOC\|HTTP MOC]]
- [[Java/Framework/Spring-Boot/MOC\|Spring Boot 安全整合]]
- [[Java/Framework/Spring-Boot/Learning/12-Spring-Security-JWT无状态认证\|Spring Security JWT 无状态认证]]
