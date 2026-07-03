---
created: 2026-06-28
tags: [mybatis, database, orm, sql]
---

# ⚪ MyBatis 数据持久层

> 对应项目文件：`mapper/UserMapper.java`、`application.yml`（MyBatis 配置）
> 关联笔记：[[07-MySQL数据库配置]] | [[11-分层架构与DTO模式]]

---

## 一、MyBatis 概述

MyBatis 是一个半自动 ORM 框架，通过 SQL 映射实现 Java 对象与数据库表之间的转换。

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>3.0.4</version>
</dependency>
```

> 💡 **MyBatis vs JPA/Hibernate：** MyBatis 是"SQL 驱动"的 ORM（开发者手写 SQL），JPA 是"对象驱动"的 ORM（自动生成 SQL）。MyBatis 适合复杂查询和已有 DBA 团队的项目。

---

## 二、@Mapper 与 @MapperScan

### @Mapper

```java
@Mapper
public interface UserMapper {
    @Select("SELECT * FROM user WHERE username = #{userName}")
    User findByUserName(String userName);
}
```

`@Mapper` 标记的接口被 MyBatis 动态创建代理对象。

### @MapperScan（入口类全局扫描）

```java
@MapperScan("top.hurrysite.mapper")
@SpringBootApplication
public class BigEventApplication { ... }
```

扫描 `top.hurrysite.mapper` 包下所有 `@Mapper` 接口。

---

## 三、注解式 SQL

### @Select — 查询

```java
@Select("SELECT * FROM user WHERE username = #{userName}")
User findByUserName(String userName);
```

`#{userName}` 是预编译占位符（`PreparedStatement`），防 SQL 注入。

### @Insert — 插入

```java
@Insert("INSERT INTO user (username, password, create_time, update_time)" +
        " VALUES (#{userName}, #{md5Password}, now(), now())")
void add(String userName, String md5Password);
```

`now()` 是 MySQL 函数，自动生成当前时间戳。

### @Update — 更新

```java
@Update("UPDATE user SET username = #{userName}, nickname = #{nickName}, " +
        "email = #{email}, update_time = now() WHERE id = #{id}")
void update(Long id, String userName, String nickName, String email);

@Update("UPDATE user SET password = #{newMd5Pwd} WHERE id = #{id}")
void updatePwd(int id, String newMd5Pwd);
```

### #{} vs ${} 参数绑定

| 方式 | 格式 | SQL 注入防护 | 适用场景 |
|------|------|-------------|---------|
| `#{}` | `#{userName}` | ✅ 安全（预编译） | 字段值（推荐全部使用） |
| `${}` | `${columnName}` | ❌ 有风险 | 动态表名/列名（谨慎使用） |

本项目全部使用 `#{}`，这是正确的安全实践。

---

## 四、驼峰命名自动映射

```yaml
# application.yml
mybatis:
  configuration:
    map-underscore-to-camel-case: true
```

**数据库命名：** `create_time`、`update_time`（下划线）
**Java 命名：** `createTime`、`updateTime`（驼峰）

启用后 MyBatis 自动完成映射：`create_time` → `createTime`。

---

## 五、POJO 实体类

```java
@Data
public class User {
    private Integer id;
    private String username;
    @JsonIgnore
    private String password;      // JSON序列化时忽略
    private String nickname;
    private String email;
    private String userPic;
    private LocalDateTime createTime;
    private LocalDateTime updateTime;
}
```

### @JsonIgnore

```java
@JsonIgnore
private String password;
```

密码在返回 JSON 时被忽略，防止密码泄漏。

---

## ★ 知识点总结

| 知识点 | 作用 | 源码位置 |
|-------|------|---------|
| `@Mapper` | 标记 MyBatis Mapper | `UserMapper.java:10` |
| `@MapperScan` | 全局扫描 Mapper | `BigEventApplication.java:12` |
| `@Select` | 查询语句 | `UserMapper.java:14` |
| `@Insert` | 插入语句 | `UserMapper.java:19` |
| `@Update` | 更新语句 | `UserMapper.java:23,31` |
| `#{}` 占位符 | 预编译参数绑定 | `UserMapper.java:14-32` |
| 驼峰命名映射 | 下划线转驼峰 | `application.yml:13` |
| `@JsonIgnore` | JSON 序列化忽略 | `pojo/User.java:15` |

> 🔗 **下一步学习：** [[07-MySQL数据库配置]] → 数据库连接配置详解
