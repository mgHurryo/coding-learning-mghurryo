---
tags:
  - http
  - guide
  - best-practice
  - rest
---

# HTTP Method 选择指南

## 如何为操作选择合适的方法

| 意图 | 推荐方法 | 示例 |
|------|----------|------|
| 获取资源 | [[GET]] | `GET /users/123` |
| 获取资源列表 | [[GET]] | `GET /users?page=2` |
| 创建资源 | [[POST]] | `POST /users` |
| 完整替换资源 | [[PUT]] | `PUT /users/123` |
| 部分更新资源 | [[PATCH]] | `PATCH /users/123` |
| 删除资源 | [[DELETE]] | `DELETE /users/123` |
| 获取资源元信息 | [[HEAD]] | `HEAD /users/123` |
| 查询支持的方法 | [[OPTIONS]] | `OPTIONS /users/123` |

## 决策流程

1. **是否只读取数据？**
   - 是 → 使用 [[GET]] 或 [[HEAD]]。
   - 否 → 继续判断。
2. **是否要创建新资源？**
   - 是 → 使用 [[POST]]。
   - 否 → 继续判断。
3. **是否要删除资源？**
   - 是 → 使用 [[DELETE]]。
   - 否 → 继续判断。
4. **是完整替换还是部分更新？**
   - 完整替换 → [[PUT]]。
   - 部分更新 → [[PATCH]]。

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

- 未认证：[[HTTP-401-Unauthorized]]
- 已认证但无权限：[[HTTP-403-Forbidden]]
- 参数语义不合法：[[HTTP-422-Unprocessable-Entity]]
- 服务端兜底异常：[[HTTP-500-Internal-Server-Error]]

## 相关概念

- [[RESTful-API-Design]]
- [[HTTP-Idempotency]]
- [[HTTP-Safety]]
- [[常见误区]]
