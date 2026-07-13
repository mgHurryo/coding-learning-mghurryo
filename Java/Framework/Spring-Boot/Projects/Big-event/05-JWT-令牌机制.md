---
title: JWT 令牌机制
description: JWT 结构与原理、JJWT 0.13 库的使用、Token 生成与解析完整流程
created: 2026-06-28
tags: [jwt, authentication, jjwt, token]
category: Spring-Boot
---

# 🟡 JWT 令牌机制

> 对应项目文件：`utils/JwtUtil.java`、`filter/JwtFilter.java`、`properties/JwtProperties.java`、`config/JwtConfig.java`
> 关联笔记：[04-Spring-Security认证授权](04-Spring-Security认证授权.md) | [08-全局异常处理](08-全局异常处理.md)

---

## 一、JWT 基本概念

**JWT（JSON Web Token）** 是一种用于双方之间安全传输信息的 JSON 对象格式的开放标准（RFC 7519）。

### 结构：三部分由 `.` 分隔

```
header.payload.signature
```

| 部分 | 示例 | 说明 |
|------|------|------|
| **Header** | `{"alg":"HS256","typ":"JWT"}` | 签名算法、Token 类型 |
| **Payload** | `{"sub":"login","userName":"admin","iat":...,"exp":...}` | 实际携带的数据（Claims） |
| **Signature** | HMACSHA256(header + "." + payload, secret) | 防篡改签名 |

### JWT 认证流程

```
1. 客户端 POST /user/login (用户名+密码)
2. 服务端验证密码 → 生成 JWT Token → 返回给客户端
3. 客户端后续请求携带 Token（Authorization: Bearer <token>）
4. 服务端 JwtFilter 解析 Token → 验证签名 → 提取用户信息
5. 验证通过 → 放行到 Controller
```

---

## 二、JJWT 0.13 库的使用

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.13.0</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.13.0</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.13.0</version>
</dependency>
```

| 模块 | 作用 |
|------|------|
| `jjwt-api` | 接口和类型定义（编译时依赖） |
| `jjwt-impl` | 实现类 |
| `jjwt-jackson` | JSON 序列化（使用 Jackson） |

---

## 三、Token 生成与解析

### Token 生成

```java
private static SecretKey key(String secret) {
    return Keys.hmacShaKeyFor(secret.getBytes(StandardCharsets.UTF_8));
}

public static String generateToken(String userName, String secret, long expire) {
    return Jwts.builder()
            .subject("login")                                   // 主题
            .claim("userName", userName)                        // 自定义声明
            .issuedAt(new Date())                               // 签发时间
            .expiration(new Date(System.currentTimeMillis() + expire))  // 过期时间
            .signWith(key(secret))                              // 签名
            .compact();                                         // 生成 Token 字符串
}
```

**JJWT API 详解：**

| 方法 | 说明 |
|------|------|
| `Jwts.builder()` | 创建 JWT Builder |
| `.subject("login")` | 设置主题（`sub` 声明） |
| `.claim("userName", "")` | 设置自定义声明 |
| `.issuedAt(new Date())` | 签发时间（`iat`） |
| `.expiration(...)` | 过期时间（`exp`） |
| `.signWith(key)` | HMAC-SHA 签名 |
| `.compact()` | 输出 Token 字符串 |

### Token 解析

```java
public static Claims parse(String secret, String token) {
    return Jwts.parser()
            .verifyWith(key(secret))       // 设置验证密钥
            .build()                        // 创建安全解析器
            .parseSignedClaims(token)       // 解析并验证签名
            .getPayload();                  // 获取 Payload（Claims）
}
```

> ⚠️ **JJWT 0.12+ API 变化：** 旧版 `setSigningKey().parseClaimsJws()` 已在 0.12+ 弃用。
> 新版使用 `parser().verifyWith(key).build().parseSignedClaims(token).getPayload()`。

### 提取用户名

```java
public static String getUserNameFromToken(String secret, String token) {
    return parse(secret, token).get("userName", String.class);
}
```

---

## 四、配置化管理 JWT

```java
@Data
@ConfigurationProperties(prefix = "jwt")
public class JwtProperties {
    private String secret;    // 签名密钥
    private long expire;      // 过期时间（毫秒）
}
```

```yaml
# application.yml
jwt:
  secret: 1a2equ8UPDhdjskjhKJH9ihlkjbxbi0983247DUOCHLKlkjkhlk987
  expire: 3600000    # 1小时
```

---

## 五、JWT 异常分类

| 异常类 | 含义 | 项目中对应处理器 |
|--------|------|----------------|
| `ExpiredJwtException` | Token 已过期 | `ExpiredJwtExceptionHandler.java` |
| `MalformedJwtException` | Token 格式错误 | `MalformedJwtExceptionHandler.java` |
| `UnsupportedJwtException` | 不支持的 Token 格式 | `UnsupportedJwtExceptionHandler.java` |
| `SignatureException` | 签名验证失败 | `SignatureExceptionHandler.java` |
| `IllegalArgumentException` | 参数错误 | `IllegalArgumentExceptionHandler.java` |

---

## ★ 知识点总结

| 知识点 | 作用 | 源码位置 |
|-------|------|---------|
| `Jwts.builder()` | 构建 JWT | `JwtUtil.java:18` |
| `.signWith(key)` | 签名 | `JwtUtil.java:23` |
| `.compact()` | 输出 Token | `JwtUtil.java:24` |
| `parser().verifyWith()` | 解析并验证 | `JwtUtil.java:28-32` |
| `parseSignedClaims().getPayload()` | 获取 Claims | `JwtUtil.java:31-32` |
| `Keys.hmacShaKeyFor()` | 创建 HMAC 密钥 | `JwtUtil.java:13-15` |
| `@ConfigurationProperties` | 配置 JWT 参数 | `JwtProperties.java:9-12` |

> 🔗 **下一步学习：** [06-MyBatis-数据持久层](06-MyBatis-数据持久层.md) → 数据库操作层
