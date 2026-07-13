---
title: HTTP Safety
description: HTTP 安全方法（Safe Methods）的定义及其在 RESTful API 设计中的意义
tags:
  - http
  - concept
  - safety
  - rest
category: Network
---

# HTTP Safety

## 定义

安全性（Safety）是指一个 HTTP 方法不会修改服务器的状态。安全的请求只读取数据，不产生副作用。

> 注意：安全不等于没有副作用，例如可能会产生日志记录，但这些对服务器资源状态没有实质性影响。

## HTTP 方法的安全性

| 方法 | 是否安全 | 说明 |
|------|----------|------|
| [GET](../Method/GET.md) | ✅ | 只读取资源 |
| [HEAD](../Method/HEAD.md) | ✅ | 只读取响应头 |
| [OPTIONS](../Method/OPTIONS.md) | ✅ | 查询通信选项 |
| [TRACE](../Method/TRACE.md) | ✅ | 回显请求 |
| [POST](../Method/POST.md) | ❌ | 创建资源，修改状态 |
| [PUT](../Method/PUT.md) | ❌ | 替换资源，修改状态 |
| [PATCH](../Method/PATCH.md) | ❌ | 修改资源，修改状态 |
| [DELETE](../Method/DELETE.md) | ❌ | 删除资源，修改状态 |

## 安全性的意义

- **可预取**：浏览器可以安全地预加载安全的资源。
- **可缓存**：安全方法的响应更适合缓存。
- **无副作用**：用户、爬虫、搜索引擎可以放心访问。

## 安全性 vs 幂等性

| 维度 | 安全性 | 幂等性 |
|------|--------|--------|
| 关注点 | 是否修改状态 | 多次请求效果是否相同 |
| GET | 安全且幂等 | 安全且幂等 |
| POST | 不安全且不幂等 | 不安全且不幂等 |
| DELETE | 不安全但幂等 | 不安全但幂等 |

## 注意事项

- 不要在 GET 请求中执行写操作，例如 `GET /deleteUser?id=1` 是严重的设计错误。
- 安全性是协议层面的约定，实际实现时仍需开发者遵守。

## 相关概念

- [HTTP-Idempotency](HTTP-Idempotency.md)
- [HTTP-Caching](HTTP-Caching.md)
- [RESTful-API-Design](RESTful-API-Design.md)
