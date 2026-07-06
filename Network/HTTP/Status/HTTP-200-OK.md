---
tags:
  - http
  - status-code
  - 200-error-code
---

# HTTP 200 OK

## 定义

`200 OK` 表示请求已成功处理，返回的响应体包含请求的结果。

## 使用场景

- [[GET]] 请求成功，返回资源数据。
- [[POST]] 请求成功，但不需要创建新资源（例如查询型 POST）。
- [[PUT]] 或 [[PATCH]] 更新成功，并返回更新后的资源。
- [[DELETE]] 删除成功，并返回删除结果。

## 示例

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "Tom"
}
```

## 注意事项

- 200 是最常见的成功状态码。
- 对于创建操作，如果返回了新资源，通常使用 [[HTTP-201-Created]] 更合适。
- 对于删除操作，如果不想返回内容，可以使用 [[HTTP-204-No-Content]]。

## 相关概念

- [[HTTP-201-Created]]
- [[HTTP-204-No-Content]]
- [[GET]]
- [[POST]]
