---
title: 接入 Mybatis
description: Spring Boot 项目中集成 MyBatis 的依赖、Mapper 扫描、数据源配置和常见实践
tags:
  - Spring
  - Spring-Boot
  - MyBatis
  - MySQL
category: Spring-Boot
---

# 05. 接入 Mybatis

[上一章：yaml 配置文件的书写与获取](04-yaml-配置文件的书写与获取.md) | [下一章：Bean 对象的扫描](06-Bean-对象的扫描.md)

MyBatis 是 SQL 驱动的持久层框架。在 Spring Boot 项目中，常用 `mybatis-spring-boot-starter` 完成自动配置，让 Mapper 接口可以作为 Spring Bean 被注入到 Service 中。

## 1. 添加依赖

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
</dependency>

<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
</dependency>
```

通用理解：

- `mybatis-spring-boot-starter` 负责 MyBatis 与 Spring Boot 的整合。
- `mysql-connector-j` 是 MySQL JDBC 驱动。
- 数据库连接配置由 Spring Boot 的 `spring.datasource.*` 提供。

## 2. 配置数据源

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/app_db?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: ${MYSQL_PASSWORD}
```

配置细节可继续看：

- [JDBC URL](JDBC-URL.md)
- [DataSource 数据源](DataSource-数据源.md)

## 3. 注册 Mapper

有两种常见方式。

### 方式一：单个 Mapper 使用 @Mapper

```java
@Mapper
public interface UserMapper {
    @Select("select * from user where username = #{userName}")
    User findByUserName(String userName);
}
```

### 方式二：入口类使用 @MapperScan

```java
@MapperScan("top.example.mapper")
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

Big-event 使用的是 `@MapperScan`。当项目中 Mapper 接口较多时，统一扫描更方便。

## 4. 注解式 SQL

常见注解：

| 注解 | 作用 | 示例场景 |
|------|------|----------|
| `@Select` | 查询 | 根据用户名查询用户 |
| `@Insert` | 新增 | 注册时插入用户 |
| `@Update` | 更新 | 修改用户资料或密码 |
| `@Delete` | 删除 | 删除记录 |

注解式 SQL 适合简单 SQL。复杂动态 SQL 更适合 XML Mapper 或其他 SQL 构造方案。

## 5. 参数绑定

MyBatis 中优先使用 `#{}` 绑定参数：

```sql
select * from user where username = #{userName}
```

`#{}` 会使用预编译参数绑定，避免把用户输入直接拼接进 SQL。

不要随意使用 `${}`，它是字符串拼接，容易引入 SQL 注入风险。

## 6. 驼峰映射

数据库字段常用下划线，Java 字段常用驼峰：

```text
create_time -> createTime
update_time -> updateTime
```

可以开启自动映射：

```yaml
mybatis:
  configuration:
    map-underscore-to-camel-case: true
```

## 7. Big-event 项目经验

- Controller 不直接访问 Mapper，而是通过 Service 调用。
- Mapper 只写数据访问逻辑，不写密码校验、Token 生成等业务规则。
- DTO 用于接口参数，POJO 用于数据库表映射。
- 数据源配置、Mapper 扫描、驼峰映射是 Spring Boot + MyBatis 项目最常见的三件套。

## 相关主题

- [MyBatis 数据访问](MyBatis-数据访问.md)
- [DataSource 数据源](DataSource-数据源.md)
- [Spring-的一般项目结构](../../../Spring-的一般项目结构.md)

## 参考资料

- [MyBatis Spring Boot Starter](https://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/)
- [MyBatis 官方文档](https://mybatis.org/mybatis-3/)



