---
title: 分层架构与 DTO 模式
description: Controller-Service-Mapper 三层架构设计、DTO 模式应用与面向接口编程实践
created: 2026-06-28
tags: [architecture, layered-architecture, dto, design-pattern]
category: Spring-Boot
---

# 🟩 分层架构与 DTO 模式

> 对应项目文件：全部 27 个 Java 源文件
> 关联笔记：[01-Spring-Boot-基础](01-Spring-Boot-基础.md) | [02-RESTful-API设计](02-RESTful-API设计.md) | [06-MyBatis-数据持久层](06-MyBatis-数据持久层.md)

---

## 一、三层架构

本项目采用经典的 **Controller → Service → Mapper** 三层架构：

```
┌──────────────────────────────────────────────────────┐
│  Controller 层（表现层）                               │
│  UserController.java → 接收HTTP请求、返回JSON响应     │
│  DTO: 接收客户端参数并完成校验                          │
└──────────────────┬───────────────────────────────────┘
                   │ 调用 Service 接口
┌──────────────────▼───────────────────────────────────┐
│  Service 层（业务逻辑层）                               │
│  UserService.java（接口） + UserServiceImpl.java（实现） │
│  核心业务处理、密码加密、Token生成                       │
└──────────────────┬───────────────────────────────────┘
                   │ 调用 Mapper
┌──────────────────▼───────────────────────────────────┐
│  Mapper 层（数据持久层）                                │
│  UserMapper.java → 注解SQL直接操作数据库                │
│  POJO: User/Category/Article 映射数据库表               │
└──────────────────┬───────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────┐
│  MySQL 数据库                                          │
│  big_event 数据库                                      │
└──────────────────────────────────────────────────────┘
```

---

## 二、各层职责

### Controller 层

- 接收 HTTP 请求 + 参数校验
- 调用 Service 层
- 返回统一响应 `Result<T>`

### Service 层

- 核心业务逻辑（注册检查、密码校验、Token 生成）
- 调用 Mapper 访问数据库
- 使用工具类（JwtUtil / Md5Util）

### Mapper 层

- 注解式 SQL 执行
- POJO 与数据库表映射
- 无业务逻辑

---

## 三、DTO 模式

| DTO 类 | 用途 | 校验注解 |
|--------|------|---------|
| `UserRegisterDTO` | 注册参数 | `@Pattern` × 2 |
| `UserLoginDTO` | 登录参数 | `@Pattern` × 2 |
| `UserInfoDTO` | 查询参数 | `@Pattern` |
| `UserUpdateDTO` | 更新用户信息 | `@Pattern`, `@NotBlank`, `@Email` |
| `UserUpdatePwdDTO` | 修改密码 | `@NotBlank` × 3, `@AssertTrue` |

### DTO vs POJO

| 对比 | DTO | POJO |
|------|-----|------|
| 位置 | `dto/` 包 | `pojo/` 包 |
| 校验注解 | ✅ 有 | ❌ 无 |
| 数据库映射 | ❌ | ✅ |
| 用途 | API 参数传递 | 数据库实体 |

---

## 四、完整的请求生命周期

```
HTTP POST /user/register?userName=admin&password=123456
    ↓
JwtFilter → 白名单放行
    ↓
UserController → @Valid 校验 DTO 参数
    ↓
UserService.register()
    ├─ UserMapper.findByUserName()  → 检查重复
    ├─ Md5Util.getMD5String()       → 加密密码
    └─ UserMapper.add()             → 写入 DB
    ↓
Result.success() → JSON 响应
```

---

## ★ 知识点总结

| 层级 | 技术 | 关键注解 |
|------|------|---------|
| Controller | RESTful API | `@RestController`, 映射注解, `@Valid` |
| Service | 业务逻辑 | `@Service`, 接口+实现 |
| Mapper | 数据持久化 | `@Mapper`, `@Select/Insert/Update` |
| POJO | 数据实体 | `@Data` |
| DTO | 数据传输 | `@Data`, 校验注解 |
| Config | 框架配置 | `@Configuration`, `@Bean` |

> 🔗 **下一步学习：** [12-项目完整分析总结](12-项目完整分析总结.md) → 项目整体总结
