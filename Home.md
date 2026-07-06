---
title: 主页
description: 个人知识库的入口页面，按领域组织
tags:
  - MOC
  - 索引
  - 主页
category: 主页
---

# 主页

欢迎来到个人知识库。本仓库按**领域驱动 + 分层聚合**的方式组织知识。

## 知识领域

| 领域 | 说明 | 入口 |
| :--- | :--- | :--- |
| ☕ **Java** | Java 基础 → 进阶 → 框架生态 | [[Java/MOC\|前往 Java]] |
| 🌐 **Network** | 传输层（TCP/UDP）→ 应用层（HTTP） | [[Network/MOC\|前往 Network]] |
| 🔒 **Security** | 认证授权（JWT/OAuth2）、传输安全 | [[Security/MOC\|前往 Security]] |
| 🗄️ **MySQL** | SQL、CRUD、索引、事务、调优与持久层实践 | [[MySQL/00-Map/MySQL-MOC\|前往 MySQL]] |
| 🧩 **Big-event** | 项目通用知识总结与落点映射 | [[Big-event通用知识总结/00-通用知识索引\|前往 Big-event]] |

## 导航路径建议

```
Java/MOC
 ├─ Foundation/  ← 基础语法、IO、网络编程
 ├─ Advanced/    ← 反射、多线程
 └─ Framework/   ← Spring、Spring Boot
       ├─ Spring-Boot/MOC
       │    ├─ Annotation/  ← 注解速查
       │    └─ Learning/    ← 学习笔记 01-09
       └─ Spring-Project-Structure

Network/MOC
 └─ HTTP/MOC
      ├─ Method/    ← 9 种请求方法
      ├─ Concept/   ← 幂等性、安全性、缓存、TLS
      ├─ Status/    ← 状态码大全
      ├─ Guide/     ← 最佳实践、设计指南
      └─ Security/  ← CORS、XST

Security/MOC
 └─ Authentication/
      ├─ JJWT        ← Java JWT 库
      └─ OAuth 2.0    ← 授权框架

MySQL/MOC
 ├─ 01-Basics/            ← 数据库、表、字段、类型、字符集、存储引擎
 ├─ 02-DDL/               ← 建库、建表、改表、约束
 ├─ 03-DML/               ← INSERT、SELECT、UPDATE、DELETE
 ├─ 04-Query/             ← WHERE、JOIN、分页、聚合、分组
 ├─ 05-Transaction-Lock/  ← 事务、隔离级别、MVCC、锁、死锁
 ├─ 06-Index/             ← 主键索引、联合索引、覆盖索引、索引失效
 ├─ 07-Performance/       ← EXPLAIN、慢查询、SQL 调优、连接池调优
 └─ 08-Java-Persistence/  ← JDBC、DataSource、MyBatis 方法实践
```

## 最近更新

- 仓库重组为 Network / Security / Java 三大领域
- [[Network/HTTP/MOC\|HTTP MOC]] 完成
- [[Security/MOC\|Security MOC]] 完成
- [[MySQL/00-Map/MySQL-MOC\|MySQL MOC]] 重构为英文目录 + 方法级 SQL / 调优知识索引
- [[Java/Framework/Spring-Boot/Learning/09-Spring-Boot自动配置的原理\|Spring-Boot 自动配置的原理]]

## 标签云

`#Java` `#Spring` `#Spring-Boot` `#Network` `#HTTP` `#Security` `#JWT` `#CORS` `#MySQL`
