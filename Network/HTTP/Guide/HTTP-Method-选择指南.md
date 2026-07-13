---
title: HTTP Method 选择指南
description: 根据业务场景选择合适的 HTTP 方法，包括安全性与幂等性对比
tags:
  - http
  - guide
  - best-practice
  - rest
category: Network
---

# HTTP Method 选择指南

## 如何为操作选择合适的方法

| 意图 | 推荐方法 | 示例 |
|------|----------|------|
| 获取资源 | [GET](../Method/GET.md) | `GET /users/123` |
| 获取资源列表 | [GET](../Method/GET.md) | `GET /users?page=2` |
| 创建资源 | [POST](../Method/POST.md) | `POST /users` |
| 完整替换资源 | [PUT](../Method/PUT.md) | `PUT /users/123` |
| 部分更新资源 | [PATCH](../Method/PATCH.md) | `PATCH /users/123` |
| 删除资源 | [DELETE](../Method/DELETE.md) | `DELETE /users/123` |
| 获取资源元信息 | [HEAD](../Method/HEAD.md) | `HEAD /users/123` |
| 查询支持的方法 | [OPTIONS](../Method/OPTIONS.md) | `OPTIONS /users/123` |

## 决策流程

1. **是否只读取数据？**
   - 是 → 使用 [GET](../Method/GET.md) 或 [HEAD](../Method/HEAD.md)。
   - 否 → 继续判断。
2. **是否要创建新资源？**
   - 是 → 使用 [POST](../Method/POST.md)。
   - 否 → 继续判断。
3. **是否要删除资源？**
   - 是 → 使用 [DELETE](../Method/DELETE.md)。
   - 否 → 继续判断。
4. **是完整替换还是部分更新？**
   - 完整替换 → [PUT](../Method/PUT.md)。
   - 部分更新 → [PATCH](../Method/PATCH.md)。

## 常见误区

- 用 GET 做删除：`GET /deleteUser?id=1` ❌
- 用 POST 做所有操作：`POST /getUser` ❌
- URL 里包含动词：`POST /createUser` ❌

## 注意事项

- 优先遵循 HTTP 语义，提高 API 可读性和可维护性。
- 考虑幂等性和安全性，选择合适的方法可以减少重复请求带来的问题。
- 复杂查询条件不适合放 URL 时，可用 POST 替代 GET。

## 来自 Big-event 的项目经验

Big-event 中的方法选择可以作为 Spring Boot CRUD 接口的简化模板：

| 接口意图 | 方法 | 选择理由 |
|----------|------|----------|
| 用户注册 | POST | 创建账号或提交注册动作，非幂等 |
| 用户登录 | POST | 提交凭据并生成 Token，非单纯查询 |
| 查询用户信息 | GET | 只读操作 |
| 更新用户资料 | PATCH | 只更新部分字段 |
| 修改密码 | PATCH | 修改用户凭据的一部分 |

注意：登录虽然“查询用户是否存在”，但它会生成 Token，属于提交认证动作，所以使用 POST 更合适。

## 和状态码的配合

- 未认证：[HTTP-401-Unauthorized](../Status/HTTP-401-Unauthorized.md)
- 已认证但无权限：[HTTP-403-Forbidden](../Status/HTTP-403-Forbidden.md)
- 参数语义不合法：[HTTP-422-Unprocessable-Entity](../Status/HTTP-422-Unprocessable-Entity.md)
- 服务端兜底异常：[HTTP-500-Internal-Server-Error](../Status/HTTP-500-Internal-Server-Error.md)

## 相关概念

- [RESTful-API-Design](../Concept/RESTful-API-Design.md)
- [HTTP-Idempotency](../Concept/HTTP-Idempotency.md)
- [HTTP-Safety](../Concept/HTTP-Safety.md)
- [常见误区](常见误区.md)
