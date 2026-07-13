---
title: MyBatis 数据访问
description: MyBatis Mapper、注解 SQL、参数绑定和字段映射实践
tags:
  - MySQL
  - MyBatis
  - Mapper
  - SQL
category: MySQL
---

# MyBatis 数据访问

MyBatis 是一个 SQL 驱动的持久层框架。它不会强行隐藏 SQL，而是让开发者通过 Mapper 接口把 Java 方法和 SQL 语句关联起来。

## MyBatis 的定位

| 对比 | MyBatis | JPA / Hibernate |
|------|---------|-----------------|
| 核心思路 | 开发者编写 SQL | 框架根据对象关系生成 SQL |
| 优势 | SQL 可控，复杂查询直观 | 基础 CRUD 和对象关系建模更完整 |
| 学习重点 | SQL、Mapper、参数绑定、结果映射 | Entity、Repository、关系映射 |

## Mapper

Mapper 是数据库访问接口。Spring Boot 项目中常见注册方式有两种：

- 在每个接口上写 `@Mapper`。
- 在启动类或配置类上使用 `@MapperScan` 批量扫描。

Big-event 使用了 Mapper 扫描，这适合 Mapper 数量逐渐增加的项目。

## 注解式 SQL

常见注解：

| 注解 | 作用 |
|------|------|
| `@Select` | 查询 |
| `@Insert` | 插入 |
| `@Update` | 更新 |
| `@Delete` | 删除 |

注解式 SQL 适合简单 SQL。复杂动态 SQL 可以考虑 XML Mapper 或专门的 SQL 构造方式。

## 参数绑定

MyBatis 中应优先使用 `#{}`：

```sql
SELECT * FROM user WHERE username = #{userName}
```

| 写法 | 含义 | 安全性 |
|------|------|--------|
| `#{param}` | 预编译参数绑定 | 推荐 |
| `${param}` | 字符串直接拼接 | 谨慎使用，有注入风险 |

## 驼峰映射

数据库常用下划线字段，例如 `create_time`；Java 常用驼峰字段，例如 `createTime`。

```yaml
mybatis:
  configuration:
    map-underscore-to-camel-case: true
```

开启后，MyBatis 可以自动完成 `create_time -> createTime` 的映射。

## Big-event 项目经验

- Mapper 层只负责 SQL，不写业务规则。
- Service 层负责密码校验、Token 生成、用户存在性判断等业务流程。
- DTO 接收接口参数，POJO 映射数据库表，二者不要混用。

## 相关主题

- [[05-接入-Mybatis]]
- [[Java/Framework/Spring-的一般项目结构]]
- [[MySQL/12-Java-Persistence/DataSource-数据源|DataSource 数据源]]
- [[Java/Advanced/反射]]
- [[Network/HTTP/Guide/幂等接口设计\|幂等接口设计]]

## 参考资料

- [MyBatis Spring Boot Starter](https://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/)
- [MyBatis 官方文档](https://mybatis.org/mybatis-3/)

