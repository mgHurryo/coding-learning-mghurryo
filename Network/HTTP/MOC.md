---
title: HTTP MOC
description: HTTP 协议知识索引，包含请求方法、状态码、核心概念与安全机制
tags:
  - HTTP
  - MOC
  - 索引
category: Network
---

# HTTP MOC

> 本文档是 HTTP 协议知识的索引地图。

## 请求方法

| 方法 | 安全 | 幂等 | 说明 |
| :--- | :---: | :---: | :--- |
| [GET](Method/GET.md) | ✅ | ✅ | 获取资源 |
| [POST](Method/POST.md) | ❌ | ❌ | 提交数据，创建资源 |
| [PUT](Method/PUT.md) | ❌ | ✅ | 整体替换资源 |
| [PATCH](Method/PATCH.md) | ❌ | 视实现 | 部分修改 |
| [DELETE](Method/DELETE.md) | ❌ | ✅ | 删除资源 |
| [HEAD](Method/HEAD.md) | ✅ | ✅ | 只获取响应头 |
| [OPTIONS](Method/OPTIONS.md) | ✅ | ✅ | 查询通信选项 |
| [TRACE](Method/TRACE.md) | ✅ | ✅ | 诊断回显 |
| [CONNECT](Method/CONNECT.md) | ❌ | ❌ | 建立隧道代理 |

## 核心概念

| 笔记 | 说明 |
| :--- | :--- |
| [幂等性](Concept/HTTP-Idempotency.md) | 多次相同请求的效果是否一致 |
| [安全性](Concept/HTTP-Safety.md) | 请求是否只读不写 |
| [缓存](Concept/HTTP-Caching.md) | 浏览器与代理缓存机制 |
| [RESTful 设计](Concept/RESTful-API-Design.md) | 基于 HTTP 方法的 API 架构 |
| [HTTP 代理](Concept/HTTP-Proxy.md) | 正向代理、反向代理 |
| [HTTPS](Concept/HTTPS.md) | 加密传输的安全版 HTTP |
| [TLS 握手](Concept/TLS-Handshake.md) | 加密连接建立流程 |

## 状态码

| 分类 | 笔记 |
| :--- | :--- |
| 2xx 成功 | [200](Status/HTTP-200-OK.md) · [201](Status/HTTP-201-Created.md) · [204](Status/HTTP-204-No-Content.md) |
| 3xx 重定向 | [304](Status/HTTP-304-Not-Modified.md) |
| 4xx 客户端错误 | [400](Status/HTTP-400-Bad-Request.md) · [401](Status/HTTP-401-Unauthorized.md) · [403](Status/HTTP-403-Forbidden.md) · [404](Status/HTTP-404-Not-Found.md) · [405](Status/HTTP-405-Method-Not-Allowed.md) · [422](Status/HTTP-422-Unprocessable-Entity.md) · [429](Status/HTTP-429-Too-Many-Requests.md) |
| 5xx 服务器错误 | [500](Status/HTTP-500-Internal-Server-Error.md) · [502](Status/HTTP-502-Bad-Gateway.md) · [503](Status/HTTP-503-Service-Unavailable.md) |

## 安全机制

| 笔记 | 说明 |
| :--- | :--- |
| [CORS 预检](Security/CORS-Preflight.md) | 浏览器跨域安全策略 |
| [XST](Security/XST.md) | 跨站追踪攻击与防范 |

## 最佳实践与指南

| 笔记 | 说明 |
| :--- | :--- |
| [方法选择指南](Guide/HTTP-Method-选择指南.md) | 不同场景下如何选择 HTTP 方法 |
| [幂等接口设计](Guide/幂等接口设计.md) | 保证网络重试安全的方案 |
| [方法安全性对比](Guide/HTTP-请求方法安全性对比.md) | 安全、幂等、可缓存三维对比 |
| [常见误区](Guide/常见误区.md) | 常见设计错误与最佳实践 |

## Spring Boot 项目实践

| 笔记 | 说明 |
| :--- | :--- |
| [RESTful API 与参数校验](../../Java/Framework/Spring-Boot/Learning/Start/10-RESTful-API与参数校验.md) | Controller、DTO、参数绑定、Bean Validation |
| [全局异常处理与统一响应](../../Java/Framework/Spring-Boot/Learning/Start/11-全局异常处理与统一响应.md) | REST API 的统一错误响应 |

## 相关主题

- [网络 MOC](../MOC.md)
- [Security MOC](../../Security/MOC.md)
- [Java 基础网络编程](../../Java/Foundation/MOC.md)
- [Spring Boot MOC](../../Java/Framework/Spring-Boot/MOC.md)
