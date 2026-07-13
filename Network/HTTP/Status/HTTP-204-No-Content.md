---
title: HTTP 204 No Content
description: 请求成功但无返回体，常用于 DELETE 操作或更新后无需返回数据的场景
tags:
  - http
  - status-code
  - 204-error-code
  - delete
category: Network
---

# HTTP 204 No Content

## 定义

`204 No Content` 表示服务器已成功处理请求，但不需要返回响应体。

## 使用场景

- [DELETE](../Method/DELETE.md) 删除成功。
- [PUT](../Method/PUT.md) 或 [PATCH](../Method/PATCH.md) 更新成功，但客户端不需要更新后的数据。
- [OPTIONS](../Method/OPTIONS.md) 预检请求成功。

## 示例

```http
DELETE /users/123 HTTP/1.1
Host: api.example.com
```

```http
HTTP/1.1 204 No Content
```

## 注意事项

- 204 响应不应包含消息体。
- 适合删除或更新后无需返回数据的场景。
- 与 200 的区别在于 200 需要返回实体内容。

## 相关概念

- [DELETE](../Method/DELETE.md)
- [PUT](../Method/PUT.md)
- [PATCH](../Method/PATCH.md)
- [OPTIONS](../Method/OPTIONS.md)
- [HTTP-200-OK](HTTP-200-OK.md)
