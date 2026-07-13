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

## 来自 Big-event 的项目经验

Big-event 的用户模块可以抽象出一套常见 REST API 经验：

| 场景 | 推荐方法 | 说明 |
|------|----------|------|
| 注册、登录 | POST | 它们是提交动作，不是单纯读取资源 |
| 查询用户信息 | GET | 读取当前用户或指定用户信息 |
| 更新资料 | PATCH | 只修改昵称、邮箱等部分字段 |
| 修改密码 | PATCH | 修改用户凭据的一部分 |

在项目里，RESTful 设计不只体现在 URL，还体现在参数和响应边界：

- DTO 接收外部输入，避免直接暴露数据库实体。
- Token 放在 `Authorization` 请求头中，而不是 URL 查询参数中。
- 成功和失败使用统一响应模型，但仍应合理搭配 HTTP 状态码。
- 参数校验失败、未认证、无权限、服务端异常应区分处理。

## 统一响应与 HTTP 状态码

常见后端会同时使用两层状态：

| 层级 | 例子 | 含义 |
|------|------|------|
| HTTP 状态码 | `200`、`401`、`422`、`500` | 协议层结果 |
| 业务响应码 | `code: 0`、`code: 1` | 应用层结果 |

学习项目可以先统一返回 `Result<T>`；正式项目中，建议让认证失败、参数错误、权限不足等场景也尽量匹配合适的 HTTP 状态码。

## 相关概念

- [[GET]]
- [[POST]]
- [[PUT]]
- [[PATCH]]
- [[DELETE]]
- [[HTTP-Method-选择指南]]
- [[10-RESTful-API与参数校验]]
- [[11-全局异常处理与统一响应]]

## 参考资料

- [Spring Framework Web MVC Reference](https://docs.spring.io/spring-framework/reference/web.html)
- [Spring Framework Error Responses](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-rest-exceptions.html)
