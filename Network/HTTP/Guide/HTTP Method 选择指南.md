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

## 相关概念

- [[RESTful API Design]]
- [[HTTP Idempotency]]
- [[HTTP Safety]]
- [[常见误区]]
