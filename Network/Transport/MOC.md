---
title: 网络传输层
description: 传输层协议基础，包括 TCP、UDP、Socket 通信原理
tags:
  - network
  - transport
  - tcp
  - udp
category: Network
---

# 网络传输层

> 传输层是 OSI 模型和 TCP/IP 模型中的核心层，负责提供端到端的通信服务。主要协议包括 **TCP**（可靠）和 **UDP**（高效）。

## TCP vs UDP

| 特性 | TCP | UDP |
| :--- | :--- | :--- |
| 连接 | 面向连接（三次握手） | 无连接 |
| 可靠性 | 可靠传输、重传机制 | 不可靠，不保证送达 |
| 有序性 | 保证数据包顺序 | 不保证 |
| 速度 | 较慢（头部大、有确认机制） | 较快 |
| 应用场景 | 网页、文件传输、邮件 | 视频直播、DNS、游戏 |

## 三次握手（建立连接）

1. **SYN**：客户端发送 SYN 包请求连接
2. **SYN+ACK**：服务端回应确认
3. **ACK**：客户端发送确认，连接建立

## 四次挥手（断开连接）

1. **FIN**：客户端发送 FIN 表示数据发送完毕
2. **ACK**：服务端确认收到 FIN
3. **FIN**：服务端发送 FIN 表示数据全部发送完毕
4. **ACK**：客户端确认收到 FIN，连接断开

## 通信方式

| 方式 | 说明 |
| :--- | :--- |
| **单播（Unicast）** | 一对一的通信 |
| **组播（Multicast）** | 一对多组，地址范围 `224.0.0.0 ~ 239.255.255.255` |
| **广播（Broadcast）** | 一对所有，地址 `255.255.255.255` |

## Java 实现

在 Java 中使用 Socket API 进行 TCP/UDP 编程，详见：

- [[Java/Foundation/网络编程\|Java 网络编程]] — Java Socket 实现 TCP/UDP

## 相关概念

- [[Network/HTTP/MOC\|HTTP MOC]] — 基于 TCP 的应用层协议
- [[Network/HTTP/Concept/TLS Handshake\|TLS 握手]] — TCP 之上的加密握手
