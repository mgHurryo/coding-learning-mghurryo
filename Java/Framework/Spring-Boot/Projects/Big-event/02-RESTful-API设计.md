---
created: 2026-06-28
tags: [restful, api, controller, java]
---

# 🔵 RESTful API 设计

> 对应项目文件：`controller/UserController.java`、`dto/*.java`、`pojo/Result.java`
> 关联笔记：[[01-Spring-Boot-基础]] | [[03-数据校验Bean-Validation]] | [[11-分层架构与-DTO-模式]]

---

## 一、@RestController 与请求映射

```java
// UserController.java
@Validated
@RestController
@RequestMapping("/user")
public class UserController {
    @Autowired
    private UserService userService;
    
    @PostMapping("/register")
    public Result<?> register(@Valid UserRegisterDTO userRegisterDTO) { ... }
    
    @PostMapping("/login")
    public Result<String> login(@Valid UserLoginDTO userLoginDTO) { ... }
    
    @GetMapping("/info")
    public Result<?> info(@Valid UserInfoDTO userInfoDTO) { ... }
    
    @PatchMapping("/update")
    public Result<?> update(@RequestBody @Valid UserUpdateDTO userUpdateDTO) { ... }
    
    @PatchMapping("/updatePwd")
    public Result<?> updatePwd(
            @RequestHeader("Authorization") String token,
            @RequestBody @Valid UserUpdatePwdDTO userUpdatePwdDTO) { ... }
}
```

### @RestController = @Controller + @ResponseBody

- `@Controller` 标记为 Spring MVC 控制器
- `@ResponseBody` 将返回值直接序列化为 JSON
- 本项目所有接口返回 JSON 格式

### HTTP 方法映射注解

| 注解 | HTTP 方法 | 本项目用途 |
|------|----------|-----------|
| `@GetMapping` | GET | 查询用户信息 |
| `@PostMapping` | POST | 注册、登录 |
| `@PatchMapping` | PATCH | 部分更新用户信息、修改密码 |

> 💡 **PATCH 语义分析：** 修改密码和更新用户信息使用 `@PatchMapping`，属于**部分更新**而非全量替换，符合 RESTful 最佳实践。

---

## 二、请求参数接收方式

本项目展示了 4 种参数接收方式：

### 方式 1：POST 表单参数（无需注解）

```java
@PostMapping("/register")
public Result<?> register(@Valid UserRegisterDTO userRegisterDTO) {
```

- 适用于 `application/x-www-form-urlencoded`
- Spring MVC 自动将参数名与 DTO 字段名匹配绑定
- 不需要 `@RequestBody`

### 方式 2：@RequestBody JSON 参数

```java
@PatchMapping("/update")
public Result<?> update(@RequestBody @Valid UserUpdateDTO userUpdateDTO) {
```

- 适用于 `application/json` 请求体
- 用 Jackson 将 JSON 反序列化为 DTO 对象

### 方式 3：@RequestHeader 请求头参数

```java
@PatchMapping("/updatePwd")
public Result<?> updatePwd(
        @RequestHeader("Authorization") String token,
        @RequestBody @Valid UserUpdatePwdDTO userUpdatePwdDTO) {
```

- 从 HTTP 请求头中提取参数
- 这里提取 `Authorization` 头的 JWT Token
- 可与 `@RequestBody` 组合使用

### 方式 4：GET 查询参数（无需注解）

```java
@GetMapping("/info")
public Result<?> info(@Valid UserInfoDTO userInfoDTO) {
```

- URL 查询字符串自动绑定到 DTO：`GET /user/info?userName=xxx`
- 不需要 `@RequestBody`

### 参数接收方式对比表

| 方式 | 注解 | 适用请求 | 数据位置 | 本项目例子 |
|------|------|---------|---------|-----------|
| 表单绑定 | 无需 | POST | 请求体（表单格式） | `/register` |
| JSON 绑定 | `@RequestBody` | POST/PATCH | 请求体（JSON） | `/update` |
| 请求头 | `@RequestHeader` | 任意 | 请求头 | 取 Token |
| 查询参数 | 无需 | GET | URL 查询字符串 | `/info` |

---

## 三、统一响应结果 `Result<T>`

```java
@NoArgsConstructor
@AllArgsConstructor
@Data
public class Result<T> {
    private Integer code;      // 状态码: 0-成功 1-失败
    private String message;    // 提示信息
    private T data;            // 响应数据（泛型）

    public static <E> Result<E> success(E data) {
        return new Result<>(0, "操作成功", data);
    }
    public static Result success() {
        return new Result(0, "操作成功", null);
    }
    public static Result error(String message) {
        return new Result(1, message, null);
    }
}
```

### 静态工厂方法模式

使用 `static` 工厂方法快速创建 Result 对象：
- `Result.success(data)` — 带数据的成功响应
- `Result.success()` — 无数据的成功响应
- `Result.error("消息")` — 失败响应

### JSON 响应示例

```json
// 登录成功
{ "code": 0, "message": "操作成功", "data": "eyJhbGciOiJIUzI1NiJ9..." }

// 注册失败
{ "code": 1, "message": "用户名已被占用", "data": null }

// 查询用户信息
{ "code": 0, "message": "操作成功", "data": { "id": 1, "username": "admin", ... } }
```

---

## ★ 知识点总结

| 知识点 | 代码位置 | 说明 |
|-------|---------|------|
| `@RestController` | `UserController.java:15` | JSON 响应控制器 |
| `@RequestMapping("/user")` | `UserController.java:16` | 类级别路径映射 |
| `@GetMapping/PostMapping/PatchMapping` | 多个方法 | HTTP 方法映射 |
| `@RequestBody` | `UserController.java:40,52` | JSON 请求体绑定 |
| `@RequestHeader` | `UserController.java:52` | 请求头绑定 |
| 表单参数自动绑定 | `UserController.java:24,29` | 无需注解绑定 |
| 泛型 `Result<T>` | `pojo/Result.java:13` | 统一响应封装 |

> 🔗 **下一步学习：** [[03-数据校验Bean-Validation]] → DTO 中如何保证数据合法性
