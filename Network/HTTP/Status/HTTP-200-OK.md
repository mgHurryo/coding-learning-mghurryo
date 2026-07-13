---
title: HTTP 200 OK
description: 请求成功的标准响应，GET 查询与同步操作的成功确认
tags:
  - http
  - status-code
  - 200-error-code
category: Network
---

# HTTP 200 OK

## 定义

`200 OK` 表示请求已成功处理，返回的响应体包含请求的结果。

## 使用场景

- [GET](../Method/GET.md) 请求成功，返回资源数据。
- [POST](../Method/POST.md) 请求成功，但不需要创建新资源（例如查询型 POST）。
- [PUT](../Method/PUT.md) 或 [PATCH](../Method/PATCH.md) 更新成功，并返回更新后的资源。
- [DELETE](../Method/DELETE.md) 删除成功，并返回删除结果。

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
- 对于创建操作，如果返回了新资源，通常使用 [HTTP-201-Created](HTTP-201-Created.md) 更合适。
- 对于删除操作，如果不想返回内容，可以使用 [HTTP-204-No-Content](HTTP-204-No-Content.md)。

## 相关概念

- [HTTP-201-Created](HTTP-201-Created.md)
- [HTTP-204-No-Content](HTTP-204-No-Content.md)
- [GET](../Method/GET.md)
- [POST](../Method/POST.md)
