---
tags:
  - http
  - rest
  - api
  - design
---

# RESTful API Design

## 定义

REST（Representational State Transfer）是一种软件架构风格，由 Roy Fielding 于 2000 年提出。RESTful API 是基于 HTTP 协议，通过 URI 标识资源，通过 HTTP 方法操作资源的设计方式。

## 核心原则

1. **资源（Resource）**：一切皆为资源，使用 URI 唯一标识，如 `/users/123`。
2. **表现层（Representation）**：资源可以有 JSON、XML 等多种表现形式。
3. **状态转移（State Transfer）**：通过 HTTP 方法对资源进行操作。
4. **无状态（Stateless）**：每个请求都包含完整信息，服务器不保存客户端状态。
5. **统一接口（Uniform Interface）**：使用标准的 HTTP 方法和状态码。

## HTTP 方法在 REST 中的应用

| 方法 | 操作 | URI 示例 | 说明 |
|------|------|----------|------|
| [[GET]] | 读取 | `/users` | 获取用户列表 |
| [[GET]] | 读取 | `/users/123` | 获取指定用户 |
| [[POST]] | 创建 | `/users` | 创建新用户 |
| [[PUT]] | 更新 | `/users/123` | 整体替换用户信息 |
| [[PATCH]] | 部分更新 | `/users/123` | 修改用户部分字段 |
| [[DELETE]] | 删除 | `/users/123` | 删除指定用户 |

## URI 设计规范

- 使用名词复数，避免动词：`/users` 而非 `/getUsers`。
- 使用小写字母和连字符：`/user-orders`。
- 层级关系用 `/` 表示：`/users/123/orders`。
- 过滤、排序、分页用查询参数：`/users?role=admin&sort=name&page=2`。

## 状态码使用建议

- `200 OK`：请求成功。
- `201 Created`：创建成功。
- `204 No Content`：删除成功或无返回内容。
- `400 Bad Request`：请求参数错误。
- `401 Unauthorized`：未认证。
- `403 Forbidden`：无权限。
- `404 Not Found`：资源不存在。
- `405 Method Not Allowed`：方法不允许。
- `500 Internal Server Error`：服务器内部错误。

## 版本控制

- URL 路径版本：`/v1/users`
- 请求头版本：`Accept: application/vnd.api.v1+json`

## 注意事项

- 避免在 URI 中使用动词，动作由 HTTP 方法表达。
- 保持接口无状态，认证信息通过 Token 传递。
- 正确返回错误信息，包含机器可读的错误码和可读描述。

## 相关概念

- [[GET]]
- [[POST]]
- [[PUT]]
- [[PATCH]]
- [[DELETE]]
- [[HTTP Method 选择指南]]
