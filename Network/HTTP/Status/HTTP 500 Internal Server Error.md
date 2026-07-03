---
tags:
  - http
  - status-code
  - 500-error-code
  - error
---

# HTTP 500 Internal Server Error

## 定义

`500 Internal Server Error` 表示服务器在处理请求时发生了意外错误。

## 使用场景

- 代码抛出未捕获的异常。
- 数据库连接失败。
- 依赖服务不可用。
- 配置错误。

## 示例

```http
HTTP/1.1 500 Internal Server Error
Content-Type: application/json

{
  "error": "Internal Server Error",
  "message": "An unexpected error occurred"
}
```

## 注意事项

- 500 是服务器端错误，客户端无法修复。
- 生产环境不应将敏感错误详情暴露给客户端，应记录到日志中。
- 避免返回堆栈跟踪给最终用户。

## 与其他服务器错误状态码的区别

| 状态码 | 含义 |
|--------|------|
| 500 | 通用服务器错误 |
| 502 | 网关错误 |
| 503 | 服务不可用 |
| 504 | 网关超时 |

## 相关概念

- [[HTTP 400 Bad Request]]
- [[HTTP 502 Bad Gateway]]
- [[HTTP 503 Service Unavailable]]

## Big-event 项目经验

`500 Internal Server Error` 适合作为服务端兜底异常响应，但不应该把内部堆栈、数据库密码、JWT secret 等敏感信息返回给前端。

Spring Boot 项目中通常使用 `@RestControllerAdvice` 和 `@ExceptionHandler` 统一处理异常，相关实现见 [[Java/Framework/Spring-Boot/Learning/11-全局异常处理与统一响应]]。
