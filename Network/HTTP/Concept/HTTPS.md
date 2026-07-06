---
tags:
  - http
  - https
  - security
  - tls
---

# HTTPS

## 定义

HTTPS（HyperText Transfer Protocol Secure）是 HTTP 的安全版本，通过 TLS/SSL 协议对通信进行加密。

## 主要作用

- **加密**：防止数据被窃听。
- **完整性**：防止数据被篡改。
- **身份验证**：通过证书验证服务器身份。

## HTTPS 工作流程

1. 客户端向服务器请求建立 HTTPS 连接。
2. 服务器返回数字证书。
3. 客户端验证证书有效性。
4. 客户端与服务器通过 [[TLS-Handshake]] 协商加密密钥。
5. 使用对称加密进行后续通信。

## HTTP vs HTTPS

| 特性 | HTTP | HTTPS |
|------|------|-------|
| 安全性 | 明文传输 | 加密传输 |
| 端口 | 80 | 443 |
| 性能 | 较快 | 略有开销 |
| SEO | 一般 | 搜索引擎更友好 |

## 注意事项

- 现代网站应默认使用 HTTPS。
- 注意证书有效期和配置，避免混合内容（Mixed Content）问题。

## 相关概念

- [[TLS-Handshake]]
- [[HTTP-Proxy]]
- [[CONNECT]]
