---
tags:
  - http
  - comparison
  - safety
  - idempotency
  - caching
---

# HTTP 请求方法安全性对比

## 对比表

| 方法 | 安全 | 幂等 | 可缓存 | 主要用途 |
|------|------|------|--------|----------|
| [[GET]] | ✅ | ✅ | ✅ | 获取资源 |
| [[HEAD]] | ✅ | ✅ | ✅ | 获取响应头 |
| [[POST]] | ❌ | ❌ | 可标记 | 创建资源 |
| [[PUT]] | ❌ | ✅ | ❌ | 整体替换资源 |
| [[PATCH]] | ❌ | 视实现 | ❌ | 部分更新资源 |
| [[DELETE]] | ❌ | ✅ | ❌ | 删除资源 |
| [[OPTIONS]] | ✅ | ✅ | ✅ | 查询支持选项 |
| [[TRACE]] | ✅ | ✅ | ❌ | 诊断回显 |
| [[CONNECT]] | ❌ | ❌ | ❌ | 建立隧道 |

## 三维解释

### 安全性（Safe）

指请求是否只读取数据而不修改服务器状态。

- 安全：GET、HEAD、OPTIONS、TRACE
- 不安全：POST、PUT、PATCH、DELETE、CONNECT

### 幂等性（Idempotent）

指多次相同请求的效果是否与一次相同。

- 幂等：GET、HEAD、PUT、DELETE、OPTIONS、TRACE
- 不幂等：POST、CONNECT
- 视实现：PATCH

### 可缓存性（Cacheable）

指响应是否可以被缓存。

- 默认可缓存：GET、HEAD、OPTIONS
- 默认不可缓存：POST、PUT、PATCH、DELETE、TRACE、CONNECT

## 设计建议

- 读操作用 GET，安全且可缓存。
- 创建操作用 POST，注意幂等性处理。
- 更新操作用 PUT（全量）或 PATCH（局部）。
- 删除操作用 DELETE，注意软删除策略。

## Big-event 场景

| Big-event 操作 | 方法 | 安全 | 幂等 | 说明 |
|----------------|------|------|------|------|
| 查询用户信息 | GET | 是 | 是 | 只读查询 |
| 注册 | POST | 否 | 否 | 创建用户或提交注册动作 |
| 登录 | POST | 否 | 否 | 提交凭据并生成 Token |
| 更新资料 | PATCH | 否 | 视实现 | 局部更新用户字段 |
| 修改密码 | PATCH | 否 | 视实现 | 局部更新凭据 |

## 相关概念

- [[HTTP Safety]]
- [[HTTP Idempotency]]
- [[HTTP Caching]]
- [[HTTP Method 选择指南]]
- [[Network/HTTP/Concept/RESTful API Design\|RESTful API Design]]
- [[Java/Framework/Spring-Boot/Learning/10-RESTful-API与参数校验\|RESTful API 与参数校验]]
