---
tags:
  - http
  - http-method
  - connect
  - proxy
  - tunnel
  - https
---

# CONNECT

## 定义

CONNECT 方法用于与目标服务器建立一个网络隧道（tunnel），通常用于 HTTPS 代理。

## 主要特点

- **建立隧道**：客户端通过代理服务器与目标服务器建立 TCP 隧道。
- **常用于 HTTPS 代理**：浏览器通过 HTTP CONNECT 请求让代理转发 HTTPS 流量。
- **非安全、非幂等**：会创建连接状态。

## 使用场景

- HTTP 代理转发 HTTPS 请求。
- 需要端到端加密时，先通过 CONNECT 建立隧道，再在该隧道上进行 TLS 握手。

## 示例

```http
CONNECT www.example.com:443 HTTP/1.1
Host: www.example.com:443
```

代理服务器如果允许连接，会返回：

```http
HTTP/1.1 200 Connection Established
```

之后客户端与目标服务器在建立的隧道上进行加密通信。

## 工作原理

1. 客户端向代理发送 CONNECT 请求。
2. 代理与目标服务器建立 TCP 连接。
3. 代理返回 `200 Connection Established`。
4. 后续数据在客户端与目标服务器之间透明转发。

## 注意事项

- CONNECT 方法通常只在代理服务器上实现。
- 滥用 CONNECT 可能导致未授权访问或代理被用于匿名访问。

## 相关概念

- [[HTTP-Proxy]]
- [[HTTPS]]
- [[TLS-Handshake]]
