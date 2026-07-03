---
created: 2026-06-28
tags: [lombok, boilerplate, java]
---

# 🔷 Lombok 代码简化

> 对应项目文件：项目中所有 POJO、DTO、Properties 类（共 10 个文件）
> 关联笔记：[[02-RESTful-API设计]] | [[06-MyBatis数据持久层]]

---

## 一、Lombok 概述

Lombok 通过**注解处理器**在编译期自动生成 getter/setter/toString/equals/hashCode 等样板代码。

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <scope>provided</scope>
</dependency>
```

---

## 二、项目使用的 Lombok 注解

### @Data — 最常用的注解

```java
@Data  // = @Getter + @Setter + @ToString + @EqualsAndHashCode + @RequiredArgsConstructor
public class User {
    private Integer id;
    private String username;
    private String password;
    // ...
}
```

**@Data 自动生成：** 8 个字段 × 2（getter/setter）= 16 个方法 + toString + equals + hashCode。

### @NoArgsConstructor — 无参构造器

```java
@NoArgsConstructor
public class Result<T> { ... }
```

Jackson 反序列化 JSON 时需要无参构造器。

### @AllArgsConstructor — 全参构造器

```java
@AllArgsConstructor
public class Result<T> {
    private Integer code;
    private String message;
    private T data;
}
```

配合静态工厂方法使用。

---

## 三、Lombok 注解分布

| 类 | Lombok 注解 | 作用 |
|----|------------|------|
| `User.java` | `@Data` | POJO 实体 |
| `Category.java` | `@Data` | 分类 POJO |
| `Article.java` | `@Data` | 文章 POJO |
| `Result<T>.java` | `@Data` + `@NoArgsConstructor` + `@AllArgsConstructor` | 统一响应 |
| 5 个 DTO | `@Data` | 数据传输对象 |
| `JwtProperties.java` | `@Data` | 配置属性 |

---

## ★ 知识点总结

| 注解 | 生成内容 | 项目中用于 |
|------|---------|-----------|
| `@Data` | Getter+Setter+ToString+EqualsAndHashCode | 所有 POJO、DTO |
| `@NoArgsConstructor` | 无参构造器 | `Result<T>` |
| `@AllArgsConstructor` | 全参构造器 | `Result<T>` |

> 🔗 **下一步学习：** [[11-分层架构与DTO模式]] → 项目整体架构设计
