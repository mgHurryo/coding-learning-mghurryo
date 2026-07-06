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
| [[Network/HTTP/Method/GET\|GET]] | ✅ | ✅ | 获取资源 |
| [[Network/HTTP/Method/POST\|POST]] | ❌ | ❌ | 提交数据，创建资源 |
| [[Network/HTTP/Method/PUT\|PUT]] | ❌ | ✅ | 整体替换资源 |
| [[Network/HTTP/Method/PATCH\|PATCH]] | ❌ | 视实现 | 部分修改 |
| [[Network/HTTP/Method/DELETE\|DELETE]] | ❌ | ✅ | 删除资源 |
| [[Network/HTTP/Method/HEAD\|HEAD]] | ✅ | ✅ | 只获取响应头 |
| [[Network/HTTP/Method/OPTIONS\|OPTIONS]] | ✅ | ✅ | 查询通信选项 |
| [[Network/HTTP/Method/TRACE\|TRACE]] | ✅ | ✅ | 诊断回显 |
| [[Network/HTTP/Method/CONNECT\|CONNECT]] | ❌ | ❌ | 建立隧道代理 |

## 核心概念

| 笔记 | 说明 |
| :--- | :--- |
| [[Network/HTTP/Concept/HTTP-Idempotency\|幂等性]] | 多次相同请求的效果是否一致 |
| [[Network/HTTP/Concept/HTTP-Safety\|安全性]] | 请求是否只读不写 |
| [[Network/HTTP/Concept/HTTP-Caching\|缓存]] | 浏览器与代理缓存机制 |
| [[Network/HTTP/Concept/RESTful-API-Design\|RESTful 设计]] | 基于 HTTP 方法的 API 架构 |
| [[Network/HTTP/Concept/HTTP-Proxy\|HTTP 代理]] | 正向代理、反向代理 |
| [[Network/HTTP/Concept/HTTPS\|HTTPS]] | 加密传输的安全版 HTTP |
| [[Network/HTTP/Concept/TLS-Handshake\|TLS 握手]] | 加密连接建立流程 |

## 状态码

| 分类 | 笔记 |
| :--- | :--- |
| 2xx 成功 | [[HTTP-200-OK\|200]] · [[HTTP-201-Created\|201]] · [[HTTP-204-No-Content\|204]] |
| 3xx 重定向 | [[HTTP-304-Not-Modified\|304]] |
| 4xx 客户端错误 | [[HTTP-400-Bad-Request\|400]] · [[HTTP-401-Unauthorized\|401]] · [[HTTP-403-Forbidden\|403]] · [[HTTP-404-Not-Found\|404]] · [[HTTP-405-Method-Not-Allowed\|405]] · [[HTTP-422-Unprocessable-Entity\|422]] · [[HTTP-429-Too-Many-Requests\|429]] |
| 5xx 服务器错误 | [[HTTP-500-Internal-Server-Error\|500]] · [[HTTP-502-Bad-Gateway\|502]] · [[HTTP-503-Service-Unavailable\|503]] |

## 安全机制

| 笔记 | 说明 |
| :--- | :--- |
| [[CORS-Preflight\|CORS 预检]] | 浏览器跨域安全策略 |
| [[Network/HTTP/Security/XST\|XST]] | 跨站追踪攻击与防范 |

## 最佳实践与指南

| 笔记 | 说明 |
| :--- | :--- |
| [[Network/HTTP/Guide/HTTP-Method-选择指南\|方法选择指南]] | 不同场景下如何选择 HTTP 方法 |
| [[Network/HTTP/Guide/幂等接口设计\|幂等接口设计]] | 保证网络重试安全的方案 |
| [[Network/HTTP/Guide/HTTP-请求方法安全性对比\|方法安全性对比]] | 安全、幂等、可缓存三维对比 |
| [[Network/HTTP/Guide/常见误区\|常见误区]] | 常见设计错误与最佳实践 |

## Spring Boot 项目实践

| 笔记 | 说明 |
| :--- | :--- |
| [[Java/Framework/Spring-Boot/Learning/10-RESTful-API与参数校验\|RESTful API 与参数校验]] | Controller、DTO、参数绑定、Bean Validation |
| [[Java/Framework/Spring-Boot/Learning/11-全局异常处理与统一响应\|全局异常处理与统一响应]] | REST API 的统一错误响应 |

## 相关主题

- [[Network/MOC\|网络 MOC]]
- [[Security/MOC\|Security MOC]]
- [[Java/Foundation/MOC\|Java 基础网络编程]]
- [[Java/Framework/Spring-Boot/MOC\|Spring Boot MOC]]
