---
title: 网络 MOC
description: 计算机网络知识索引，从传输层到应用层协议
tags:
  - Network
  - MOC
  - 索引
category: Network
---

# 网络 MOC

> 计算机网络协议栈的知识地图，涵盖传输层（TCP/UDP）与应用层（HTTP）协议。

## 传输层

| 笔记 | 说明 |
| :--- | :--- |
| [[Network/Transport/MOC\|传输层 MOC]] | TCP/UDP 编程、Socket、单播/组播/广播 |

## 应用层 — HTTP

| 子域 | 入口 | 说明 |
| :--- | :--- | :--- |
| **请求方法** | [[Network/HTTP/MOC\|HTTP MOC]] | GET、POST、PUT、PATCH、DELETE、HEAD、OPTIONS 等 |
| **核心概念** | 幂等性 · 安全性 · 缓存 | HTTP 协议设计的重要语义 |
| **状态码** | 200 · 201 · 204 · 4xx · 5xx | 各类状态码详解 |
| **安全机制** | CORS · XST | 浏览器安全策略与攻击防范 |
| **项目实践** | [[Network/HTTP/Concept/RESTful API Design\|RESTful API]] · [[Network/HTTP/Guide/HTTP Method 选择指南\|方法选择]] | Spring Boot 后端接口设计经验 |

## 相关主题

- [[Java/Foundation/MOC\|Java 基础网络编程]] — Java Socket API 实现
- [[Security/MOC\|Security MOC]] — OAuth2、JWT 等上层认证协议
