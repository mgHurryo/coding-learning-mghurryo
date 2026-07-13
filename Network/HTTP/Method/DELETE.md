---
title: DELETE
description: HTTP DELETE 方法，用于删除指定资源，幂等但非安全
tags:
  - http
  - http-method
  - delete
  - rest
category: Network
---

# DELETE

## 定义

DELETE 方法用于删除指定的资源。

## 主要特点

- **非安全（Not Safe）**：会修改服务器状态。
- **幂等（Idempotent）**：多次删除同一个已删除的资源，结果应相同。
- **可缓存**：DELETE 响应通常不可缓存。
- **参数传递**：通常通过 URL 路径指定要删除的资源标识。

## 使用场景

- 删除用户、文章、订单等资源。
- 撤销某个已创建的资源。

## 示例

```http
DELETE /users/123 HTTP/1.1
Host: api.example.com
```

## 常见响应状态码

- `204 No Content`：删除成功，无返回体。
- `200 OK`：删除成功，并返回删除结果或剩余信息。
- `404 Not Found`：要删除的资源不存在。

## 注意事项

- 删除操作通常不可逆，建议配合确认机制或软删除策略。
- 幂等性意味着重复删除不应报错或产生新的副作用。

## 相关概念

- [HTTP-Idempotency](../Concept/HTTP-Idempotency.md)
- [RESTful-API-Design](../Concept/RESTful-API-Design.md)
- [HTTP-204-No-Content](../Status/HTTP-204-No-Content.md)
