---
title: HTTP Proxy
description: HTTP 代理服务器的类型、工作原理及常见配置方式
tags:
  - http
  - proxy
  - network
category: Network
---

# HTTP Proxy

## 定义

HTTP 代理是位于客户端和目标服务器之间的中间服务器，负责转发客户端的 HTTP 请求。

## 主要类型

- **正向代理**：代理客户端访问外部网络，常用于翻墙、匿名访问。
- **反向代理**：代理服务器接收客户端请求，转发给后端服务器，如 Nginx、HAProxy。
- **透明代理**：客户端无感知，通常用于网络监控或缓存。

## 常见功能

- 负载均衡
- 缓存加速
- SSL 终止
- 访问控制
- 日志记录

## 与 CONNECT 的关系

当客户端需要通过代理访问 HTTPS 网站时，会先发送 [[CONNECT]] 请求与目标服务器建立隧道，然后在隧道中进行 TLS 加密通信。

## 相关概念

- [[CONNECT]]
- [[HTTPS]]
- [[TLS-Handshake]]
- [[HTTP-Caching]]
