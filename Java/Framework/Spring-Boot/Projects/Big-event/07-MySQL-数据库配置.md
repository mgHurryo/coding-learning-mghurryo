---
title: MySQL 数据库配置
description: DataSource 配置详解、JDBC URL 参数与 Spring Boot 数据源自动装配
created: 2026-06-28
tags: [mysql, datasource, configuration]
category: Spring-Boot
---

# 🟠 MySQL 数据库配置

> 对应项目文件：`application.yml`（datasource 配置）
> 关联笔记：[[06-MyBatis-数据持久层]] | [[01-Spring-Boot-基础]]

---

## 一、DataSource 配置

```yaml
# application.yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/big_event?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: a987654321@
```

### 配置项详解

| 配置项 | 值 | 说明 |
|--------|-----|------|
| `driver-class-name` | `com.mysql.cj.jdbc.Driver` | MySQL 8.x 驱动类 |
| `url` | `jdbc:mysql://localhost:3306/big_event?` | 连接地址 |
| `username` | `root` | 数据库用户名 |
| `password` | `****` | 数据库密码 |

### JDBC URL 分解

```
jdbc:mysql://localhost:3306/big_event?useUnicode=true&characterEncoding=UTF-8
  ├── jdbc:mysql://    → JDBC协议
  ├── localhost:3306   → 主机:端口
  ├── big_event        → 数据库名
  └── ?useUnicode=true&characterEncoding=UTF-8  → UTF-8编码保障
```

---

## 二、连接驱动

```xml
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
</dependency>
```

> 💡 **`mysql-connector-j`** 是 MySQL 官方驱动的**新名称**。旧名 `mysql-connector-java` 已重命名。Spring Boot 3.x 自动引入兼容版本。

---

## ★ 知识点总结

| 知识点 | 作用 | 位置 |
|-------|------|------|
| DataSource 配置 | 数据库连接参数 | `application.yml:2-6` |
| MySQL 8.x 驱动 | 连接 MySQL | `pom.xml:36-38` |
| UTF-8 URI 参数 | 支持中文 | `application.yml:4` |

> 🔗 **下一步学习：** [[08-全局异常处理]] → 异常的统一处理机制
