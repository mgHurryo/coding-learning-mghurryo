---
tags:
  - http
  - status-code
  - 422-error-code
  - error
---

# HTTP 422 Unprocessable Entity

## 定义

`422 Unprocessable Entity` 表示服务器理解请求的内容类型和语法，但无法处理其中包含的语义错误。

## 使用场景

- 请求体格式正确，但业务校验失败。
- 必填字段缺失或字段值不符合业务规则。

## 422 vs 400

| 状态码 | 含义 | 场景 |
|--------|------|------|
| 400 | 请求语法错误 | JSON 格式错误、参数类型错误 |
| 422 | 语义错误 | 格式正确但业务规则不满足 |

## 示例

```http
POST /orders HTTP/1.1
Content-Type: application/json

{
  "productId": "abc",
  "quantity": -1
}
```

```http
HTTP/1.1 422 Unprocessable Entity
Content-Type: application/json

{
  "error": "Unprocessable Entity",
  "message": "quantity must be greater than 0"
}
```

## 相关概念

- [[HTTP 400 Bad Request]]
- [[POST]]
- [[RESTful API Design]]
