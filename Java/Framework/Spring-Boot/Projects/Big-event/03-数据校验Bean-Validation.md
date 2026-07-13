---
title: Bean Validation 数据校验
description: @Valid 与 @Validated 区别、常用校验注解（@Pattern、@NotBlank、@Email）与自定义校验方法
created: 2026-06-28
tags: [validation, bean-validation, java]
category: Spring-Boot
---

# 🟣 Bean Validation 数据校验

> 对应项目文件：`dto/*.java`（5 个 DTO 文件）
> 关联笔记：[02-RESTful-API设计](02-RESTful-API设计.md) | [08-全局异常处理](08-全局异常处理.md)

---

## 一、Bean Validation 概述

本项目在 5 个 DTO 类上广泛使用 **Jakarta Bean Validation** 进行数据校验。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

Spring Boot 自动引入 **Hibernate Validator**（该规范的参考实现）。

---

## 二、常用校验注解详解

### 1. @Pattern — 正则表达式校验

**应用于用户名和密码：**

```java
@Pattern(regexp = "^\\w{5,16}$")
private String userName;      // 5-16位单词字符

@Pattern(regexp = "^\\w{5,16}$")
private String password;
```

**应用于昵称：**

```java
@Pattern(regexp = "^\\w{1,16}$")
private String nickName;
```

> 💡 `\\w` 在 Java 中等于 `[a-zA-Z0-9_]`。注意 Java 字符串中需要**双重转义**（`\\\\` 代表正则中的 `\\`）。

### 2. @NotBlank — 非空校验

```java
@NotBlank(message = "邮箱不能为空")
private String email;

@NotBlank(message = "新密码不能为空")
private String newPwd;
```

| 空值注解 | 校验规则 | 推荐度 |
|---------|---------|-------|
| `@NotNull` | 值不为 null | ⭐ |
| `@NotEmpty` | 不为 null 且不为空字符串 | ⭐⭐ |
| `@NotBlank` | 不为 null、空字符串、纯空格 | **⭐⭐⭐（推荐）** |

### 3. @Email — 邮箱格式校验

```java
@Email(message = "邮箱格式不正确")
private String email;
```

自动校验 email 是否符合 `xxx@domain.com` 格式，不验证域名是否真实存在。

### 4. @AssertTrue — 自定义方法级校验

```java
// UserUpdatePwdDTO.java
@AssertTrue(message = "两次输入的密码不一致")
private boolean isPwdSame() {
    if (newPwd == null || reNewPwd == null) {
        return true;  // null 暂不处理，交给 @NotBlank
    }
    return newPwd.equals(reNewPwd);
}
```

> 💡 **@AssertTrue 独有特性：**
> - 修饰**方法**而非字段，返回值需为 `boolean`
> - 方法名必须以 `is` 开头
> - 用于跨字段校验（如密码一致性确认）
> - null 返回 `true` = "暂时放行"，避免与 `@NotBlank` 冲突

---

## 三、@Valid 与 @Validated 的区别

| 特性 | `@Valid`（Jakarta） | `@Validated`（Spring） |
|------|--------------------|----------------------|
| 来源 | Jakarta EE 标准 | Spring 注解 |
| 分组校验 | ❌ 不支持 | ✅ 支持 |
| 嵌套校验 | ✅ 支持 | ❌ 不支持 |
| 使用位置 | 方法参数 | **类级别**或方法参数 |
| 本项目用途 | 触发 DTO 字段校验 | 启用 Service 方法校验 |

**Controller 层使用 `@Valid`：**
```java
public Result<?> register(@Valid UserRegisterDTO dto) { ... }
```

**Service 层使用 `@Validated`：**
```java
@Validated
@Service
public class UserServiceImpl implements UserService { ... }
```

---

## ★ 知识点总结

| 校验注解 | 作用 | 项目中使用处 |
|---------|------|------------|
| `@Pattern(regexp)` | 正则格式校验 | 用户名、密码、昵称 |
| `@NotBlank` | 非空校验 | 邮箱、新旧密码 |
| `@Email` | 邮箱格式校验 | 邮箱 |
| `@AssertTrue` | 自定义方法校验 | 确认密码一致性 |
| `@Valid` | 触发对象校验 | Controller 参数 |
| `@Validated` | 类级启用校验 | Service 类 |

> 🔗 **下一步学习：** [04-Spring-Security认证授权](04-Spring-Security认证授权.md) → JWT 认证与权限控制
