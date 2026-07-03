---
title: 主页
description: 个人知识库的入口页面，按领域组织
tags:
  - MOC
  - 索引
  - 主页
category: 主页
---

# 主页

欢迎来到个人知识库。本仓库按**领域驱动 + 分层聚合**的方式组织知识。

## 知识领域

| 领域 | 说明 | 入口 |
| :--- | :--- | :--- |
| ☕ **Java** | Java 基础 → 进阶 → 框架生态 | [[Java/MOC\|前往 Java]] |
| 🌐 **Network** | 传输层（TCP/UDP）→ 应用层（HTTP） | [[Network/MOC\|前往 Network]] |
| 🔒 **Security** | 认证授权（JWT/OAuth2）、传输安全 | [[Security/MOC\|前往 Security]] |

## 导航路径建议

```
Java/MOC
 ├─ Foundation/  ← 基础语法、IO、网络编程
 ├─ Advanced/    ← 反射、多线程
 └─ Framework/   ← Spring、Spring Boot
       ├─ Spring-Boot/MOC
       │    ├─ Annotation/  ← 注解速查
       │    └─ Learning/    ← 学习笔记 01-09
       └─ Spring-Project-Structure

Network/MOC
 └─ HTTP/MOC
      ├─ Method/    ← 9 种请求方法
      ├─ Concept/   ← 幂等性、安全性、缓存、TLS
      ├─ Status/    ← 状态码大全
      ├─ Guide/     ← 最佳实践、设计指南
      └─ Security/  ← CORS、XST

Security/MOC
 └─ Authentication/
      ├─ JJWT        ← Java JWT 库
      └─ OAuth 2.0    ← 授权框架
```

## 最近更新

- 仓库重组为 Network / Security / Java 三大领域
- [[Network/HTTP/MOC\|HTTP MOC]] 完成
- [[Security/MOC\|Security MOC]] 完成
- [[Java/Framework/Spring-Boot/Learning/09-Spring-Boot自动配置的原理\|Spring-Boot 自动配置的原理]]

## 标签云

`#Java` `#Spring` `#Spring-Boot` `#Network` `#HTTP` `#Security` `#JWT` `#CORS`
