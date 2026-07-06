---
tags:
  - http
  - http-method
  - put
  - rest
---

# PUT

## 定义

PUT 方法用于整体更新或替换指定的资源。如果资源不存在，某些实现会根据客户端提供的标识创建新资源。

## 主要特点

- **非安全（Not Safe）**：会修改服务器状态。
- **幂等（Idempotent）**：多次相同的 PUT 请求，结果与一次相同。
- **可缓存**：PUT 响应通常不可缓存。
- **参数传递**：待替换的完整资源数据放在请求体中。

## 使用场景

- 完整替换某个资源，例如更新用户的全部字段。
- 在已知资源标识的情况下创建资源（较少见）。

## 示例

```http
PUT /users/123 HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "id": 123,
  "name": "Tom",
  "email": "tom@example.com",
  "age": 25
}
```

## PUT vs PATCH

| 维度 | PUT | PATCH |
|------|-----|-------|
| 更新范围 | 整体替换 | 局部修改 |
| 幂等性 | 幂等 | 视实现而定 |
| 数据量 | 通常较大 | 通常较小 |
| 风险 | 可能覆盖未提供的字段 | 语法错误可能导致不一致 |

## 注意事项

- 使用 PUT 时，请求体应包含资源的完整表示，未提供的字段通常会被清空或设为默认值。
- 如果只想更新部分字段，应使用 [[PATCH]]。

## 相关概念

- [[PATCH]]
- [[HTTP-Idempotency]]
- [[RESTful-API-Design]]
