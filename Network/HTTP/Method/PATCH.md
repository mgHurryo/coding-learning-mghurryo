---
tags:
  - http
  - http-method
  - patch
  - rest
---

# PATCH

## 定义

PATCH 方法用于对资源进行部分修改。与 PUT 不同，PATCH 只更新请求体中提供的字段，而不会影响资源的其他字段。

## 主要特点

- **非安全（Not Safe）**：会修改服务器状态。
- **幂等性**：RFC 5789 建议 PATCH 应设计为幂等，但实际实现中可能不是。
- **可缓存**：PATCH 响应通常不可缓存。
- **参数传递**：部分更新的字段放在请求体中。

## 使用场景

- 只更新资源的某几个字段。
- 减少不必要的数据传输。

## 示例

```http
PATCH /users/123 HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "email": "newtom@example.com"
}
```

## PATCH 格式

常见的 PATCH 数据格式包括：

- `application/json`：直接提供要更新的字段。
- `application/json-patch+json`：JSON Patch 标准（RFC 6902）。
- `application/merge-patch+json`：JSON Merge Patch（RFC 7396）。

## 注意事项

- 设计 PATCH 接口时应尽量保证幂等，避免重试导致意外结果。
- 需要明确处理字段缺失、类型错误等情况。

## Big-event 场景

Big-event 中更新用户资料和修改密码适合 PATCH，因为它们只修改资源的一部分：

- 更新资料只修改昵称、邮箱等字段。
- 修改密码只修改用户凭据，不替换整个用户资源。

这类接口应结合 DTO 和 Bean Validation，明确处理字段缺失、格式错误和跨字段校验。

## 相关概念

- [[PUT]]
- [[HTTP-Idempotency]]
- [[RESTful-API-Design]]
- [[Java/Framework/Spring-Boot/Learning/10-RESTful-API与参数校验\|RESTful API 与参数校验]]
- [[Security/Authentication/密码存储实践\|密码存储实践]]
