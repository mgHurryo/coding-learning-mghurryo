---
tags:
  - java
  - jwt
  - auth
  - security
  - library
  - tutorial
---

# JJWT 笔记

> **JJWT**（Java JWT）是一个纯 Java 实现的 JSON Web Token（JWT）库，由 Okta 维护。官方仓库：[github.com/jwtk/jjwt](https://github.com/jwtk/jjwt)。

---

## 依赖

### Maven

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.12.6</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.12.6</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.12.6</version>
    <scope>runtime</scope>
</dependency>
```

### Gradle

```groovy
implementation 'io.jsonwebtoken:jjwt-api:0.12.6'
runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.12.6'
runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.12.6'
```

**版本说明**：`jjwt-api` 编译时即可，`jjwt-impl` 和 `jjwt-jackson` 运行时加载。三个 artifact 版本号必须一致。

---

## 核心 API

JJWT 0.12.x 引入了 **Builder / Parser** 链式 API，告别了旧版的 `HS512` 魔法字符串。

| 类/接口 | 作用 |
|---------|------|
| `Jwts.builder()` | 构建 JWT（编码） |
| `Jwts.parser().verifyWith(key).build()` | 解析并验证 JWT（解码） |
| `Jwts.SIG.HS256` | 签名算法常量 |
| `SecretKey` | 对称密钥（HS 系列） |
| `KeyPair` | 非对称密钥对（RS / ES 系列） |

---

## 基本用法

### 1. 创建 JWT（签名）

```java
import io.jsonwebtoken.Jwts;
import javax.crypto.SecretKey;
import io.jsonwebtoken.security.Keys;
import java.util.Date;

// 生成足够长的密钥（HS256 需要 256 位）
SecretKey key = Keys.hmacShaKeyFor("my-very-long-secret-key-at-least-256-bits-long!!".getBytes());

String jwt = Jwts.builder()
    .subject("user123")
    .issuer("my-app")
    .issuedAt(new Date())
    .expiration(new Date(System.currentTimeMillis() + 3600_000)) // 1 小时后过期
    .claim("role", "admin")
    .claim("tenantId", "t001")
    .signWith(key)                // JJWT 自动选择最佳算法
    .compact();                   // 序列化为紧凑字符串
```

### 2. 解析 JWT（验证）

```java
import io.jsonwebtoken.JwtParser;
import io.jsonwebtoken.Claims;

JwtParser parser = Jwts.parser()
    .verifyWith(key)              // 与签名时相同的密钥
    .requireIssuer("my-app")      // （可选）校验 issuer
    .build();

Claims claims = parser.parseSignedClaims(jwt).getPayload();

String subject = claims.getSubject();
String role    = claims.get("role", String.class);
```

### 3. 捕获解析异常

```java
try {
    Claims claims = Jwts.parser()
        .verifyWith(key)
        .build()
        .parseSignedClaims(token)
        .getPayload();
} catch (JwtException e) {
    // 签名验证失败 / Token 过期 / 格式错误 → 拒绝
    throw new SecurityException("Invalid token", e);
}
```

| 异常 | 含义 |
|------|------|
| `ExpiredJwtException` | Token 已过期 |
| `UnsupportedJwtException` | JWT 格式不支持 |
| `MalformedJwtException` | JWT 字符串格式错误 |
| `SignatureException` | 签名不匹配 |
| `IllegalArgumentException` | Token 为 null / 空字符串 |

---

## 签名算法

JJWT 通过 `Jwts.SIG` 子类管理所有支持的签名算法。

### 对称算法（HMAC）

| 算法 | 最短密钥长度 | JJWT 常量 |
|------|-------------|-----------|
| HS256 | 256 位（32 字节） | `Jwts.SIG.HS256` |
| HS384 | 384 位（48 字节） | `Jwts.SIG.HS384` |
| HS512 | 512 位（64 字节） | `Jwts.SIG.HS512` |

```java
// 自动生成安全的随机密钥
SecretKey key = Keys.secretKeyFor(SignatureAlgorithm.HS256);
// 或从已有密钥构建
SecretKey key = Keys.hmacShaKeyFor(base64EncodedSecretBytes);
```

### 非对称算法（RSA / ECDSA）

| 算法 | JJWT 常量 |
|------|-----------|
| RS256 | `Jwts.SIG.RS256` |
| RS384 | `Jwts.SIG.RS384` |
| RS512 | `Jwts.SIG.RS512` |
| ES256 | `Jwts.SIG.ES256` |
| ES384 | `Jwts.SIG.ES384` |
| ES512 | `Jwts.SIG.ES512` |

```java
// 生成 RSA 密钥对
KeyPair keyPair = Keys.keyPairFor(SignatureAlgorithm.RS256);

// 签名用私钥
String jwt = Jwts.builder()
    .subject("user123")
    .signWith(keyPair.getPrivate())
    .compact();

// 验证用公钥
Claims claims = Jwts.parser()
    .verifyWith(keyPair.getPublic())
    .build()
    .parseSignedClaims(jwt)
    .getPayload();
```

---

## 自定义 Claims

```java
// 写入时
Jwts.builder()
    .claim("roles", List.of("admin", "editor"))
    .claim("metadata", Map.of("ip", "10.0.0.1", "device", "mobile"))
    // …

// 读取时
List<String> roles = claims.get("roles", List.class);
Map<String, String> metadata = claims.get("metadata", Map.class);
```

JJWT 会自动将复杂类型序列化为 JSON（依赖 jackson）。

---

## Header 自定义

```java
String jwt = Jwts.builder()
    .header()
        .keyId("my-key-id")               // kid
        .add("custom", "value")
        .and()
    .subject("user123")
    .signWith(key)
    .compact();
```

---

## 常见场景：Bearer Token 认证

结合 Spring Boot 的拦截器模式：

```java
@Component
public class JwtFilter extends OncePerRequestFilter {

    private final SecretKey key = Keys.hmacShaKeyFor(secretBytes);

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain chain) {

        String authHeader = request.getHeader("Authorization");
        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            chain.doFilter(request, response);
            return;
        }

        String token = authHeader.substring(7);

        try {
            Claims claims = Jwts.parser()
                .verifyWith(key)
                .build()
                .parseSignedClaims(token)
                .getPayload();

            // 将用户信息注入 SecurityContext
            UsernamePasswordAuthenticationToken auth =
                new UsernamePasswordAuthenticationToken(
                    claims.getSubject(), null, getAuthorities(claims));
            SecurityContextHolder.getContext().setAuthentication(auth);

        } catch (JwtException e) {
            SecurityContextHolder.clearContext();
        }

        chain.doFilter(request, response);
    }
}
```

---

## 版本迁移：0.11.x → 0.12.x

JJWT 0.12 是重大的重构版本，API 变化较大。

| 旧 API（0.11.x） | 新 API（0.12.x） |
|------------------|------------------|
| `setSubject()` | `subject()` |
| `signWith(Key, SignatureAlgorithm)` | `signWith(Key)`（自动推导） |
| `parseClaimsJws(jwt)` | `parseSignedClaims(jwt)` |
| `getBody()` | `getPayload()` |
| `SignatureAlgorithm.HS256` | `Jwts.SIG.HS256` |

> 0.12.x 不再支持 `signWith(SecretKey, SignatureAlgorithm.HS512)` 这种显式算法签名——算法由密钥类型自动推导。

---

## Big-event 项目经验

Big-event 使用 JJWT 生成和解析登录 Token，可以抽象出以下通用经验：

- `jjwt-api`、`jjwt-impl`、`jjwt-jackson` 三个 artifact 版本应保持一致。
- JWT 密钥和过期时间应来自配置，不要硬编码在业务方法中。
- Token 解析应捕获过期、格式错误、签名错误等异常，并交给统一异常响应处理。
- Spring Boot 项目中，JJWT 通常和 Spring Security Filter 组合使用。

Big-event 使用的是 JJWT 0.13.x 系列；本笔记原示例以 0.12.x API 为主，实际项目应以当前依赖版本的官方 README / Javadoc 为准。

## 注意事项

1. **密钥长度**：HMAC 密钥必须满足算法最短长度要求，过短会抛 `WeakKeyException`。
2. **时钟偏差**：对于跨系统场景，可用 `allowedClockSkewSeconds(60)` 允许小范围时钟偏差。
3. **序列化器**：JJWT 默认使用 Jackson，也可配置 Gson 或自定义序列化器。
4. **Base64 编码密钥**：生产环境密钥应通过环境变量或密钥管理服务（Vault）注入，不硬编码。
5. **JWT 无状态**：一旦签发，无法主动失效。若要支持登出，需配合黑名单（Redis / DB）。

## 相关概念

- [[Security/Authentication/JWT-无状态认证|JWT 无状态认证]]
- [[Security/Authentication/Bearer-Authentication|Bearer Authentication]]
- [[HTTP-401-Unauthorized|HTTP-401-Unauthorized]]
- [[Security/Authentication/OAuth-2.0|OAuth 2.0]]
- [[Java/Framework/Spring-Boot/Learning/12-Spring-Security-JWT无状态认证|Spring Security JWT 无状态认证]]

## 参考资料

- [JJWT GitHub](https://github.com/jwtk/jjwt)
- [JJWT API Javadoc](https://javadoc.io/doc/io.jsonwebtoken/jjwt-api/latest/index.html)
