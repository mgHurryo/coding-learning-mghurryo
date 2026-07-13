---
title: Lombok 与 DTO 模式
description: Spring Boot 项目中使用 Lombok 简化 DTO、POJO、配置属性类的样板代码
tags:
  - Spring-Boot
  - Lombok
  - DTO
  - Architecture
category: Spring-Boot
---

# 13. Lombok 与 DTO 模式

Lombok 通过编译期注解处理器生成 getter、setter、构造器等样板代码。DTO 模式用于隔离接口参数模型和数据库实体模型。

## 1. Lombok 常见注解

| 注解 | 作用 |
|------|------|
| `@Data` | 生成 getter、setter、toString、equals、hashCode |
| `@NoArgsConstructor` | 生成无参构造器 |
| `@AllArgsConstructor` | 生成全参构造器 |
| `@Getter` / `@Setter` | 只生成访问器方法 |

Big-event 中 DTO、POJO、配置属性类都使用 Lombok 减少样板代码。

## 2. DTO 的职责

DTO 适合表示接口输入或输出：

- 注册参数 DTO。
- 登录参数 DTO。
- 更新资料 DTO。
- 修改密码 DTO。
- 用户信息响应 DTO。

DTO 可以承载 Bean Validation 注解，例如 `@NotBlank`、`@Email`、`@Pattern`。

## 3. DTO 与 POJO 的边界

| 对比项 | DTO | POJO / Entity |
|--------|-----|---------------|
| 主要依据 | 接口场景 | 数据库结构 |
| 是否暴露给前端 | 可以按需暴露 | 不建议直接暴露全部字段 |
| 是否带校验 | 常见 | 较少 |
| 是否包含敏感字段 | 应避免 | 可能包含密码哈希等内部字段 |

## 4. 实战注意点

- 不要把数据库实体直接作为所有接口入参。
- 返回用户信息时不要暴露密码哈希。
- `@Data` 会生成 `toString`，敏感字段要谨慎打印。
- 复杂领域对象的 `equals` 和 `hashCode` 不要盲目依赖默认生成。

## 相关主题

- [[Java/Framework/Spring-的一般项目结构]]
- [[10-RESTful-API与参数校验]]

## 参考资料

- [Project Lombok](https://projectlombok.org/)
- [Spring Framework Web MVC Reference](https://docs.spring.io/spring-framework/reference/web.html)

