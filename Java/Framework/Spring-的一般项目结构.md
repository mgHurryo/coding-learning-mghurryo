---
title: Spring 的一般项目结构
tags:
  - Spring
  - Architecture
  - DTO
  - Layered-Architecture
---
```text

top.hurrysite
	|
	|--- controller(控制层, 负责调用services层)
	|--- config(配置层)
	|--- dto(数据包装交互层, 负责接收前端发来的数据并校验数据合法)
	|		|--- request(前端发送参数)
	|		|--- response(返回参数)
	|
	|--- exception(错误处理层)
	|--- filter(流量清洗层, JWT解析/请求合法性/跨域/黑名单(能不能进系统))
	|--- interceptor(更细的权限, 登录态/role/admin/user(能不能用功能))
	|--- mapper(数据库操作层)
	|--- entity(数据包装层)
	|--- service(服务层, 负责具体业务逻辑(通常放接口))
	|		|---impl(具体实现类)
	|
	|--- utils(工具层)
	|--- Application(启动类)
```

## 各层职责

| 层级 | 职责 | 不应该做的事 |
|------|------|--------------|
| `controller` | 接收 HTTP 请求、参数校验、返回响应 | 直接写 SQL、承载复杂业务 |
| `service` | 组织业务流程、调用数据层、处理业务规则 | 解析 HTTP 请求细节 |
| `mapper` | 执行 SQL、访问数据库 | 做密码校验、生成 Token |
| `dto` | 接收或返回接口数据 | 直接绑定数据库表全部字段 |
| `entity` / `pojo` | 表示数据库实体或领域对象 | 直接暴露给所有接口 |
| `config` | 框架配置、Bean 注册、第三方组件配置 | 写业务流程 |
| `filter` | 进入 Spring MVC 前的请求过滤 | 编排复杂业务功能 |
| `exception` | 统一异常响应 | 暴露内部堆栈和敏感配置 |
| `utils` | 无状态工具方法 | 保存请求上下文或业务状态 |

## Big-event 项目经验

Big-event 的请求链路可以抽象为：

```text
HTTP 请求
  -> JwtFilter 处理认证
  -> Controller 接收参数并触发校验
  -> Service 编排注册、登录、修改密码等业务
  -> Mapper 执行 SQL
  -> Result 统一返回 JSON
```

这说明：

- Controller 只做请求入口，不直接操作数据库。
- Service 是业务规则的主场，例如用户存在性判断、密码校验、Token 生成。
- Mapper 只处理 SQL 和对象映射。
- DTO 用来接收外部输入，并配合 Bean Validation 做校验。

## 可扩展切分方式

为了让后续项目也能复用这套知识，建议按以下层次组织笔记：

| 层次 | 放什么 | 示例 |
|------|--------|------|
| 领域基础知识 | 协议、数据库、安全概念 | [[Network/HTTP/Concept/RESTful-API-Design]][[MySQL/12-Java-Persistence/JDBC-URL\|JDBC URL]]、[[Security/Authentication/JWT-无状态认证]] |
| 框架实现知识 | Spring Boot、MyBatis、Validation 等实现 | [[10-RESTful-API与参数校验]] |
| 集成模式知识 | 多个技术组合后的工程模式 | [[12-Spring-Security-JWT无状态认证]] |
| 项目案例知识 | 某个项目如何应用这些知识 | [[Java/Framework/Spring-Boot/Projects/Big-event/12-项目完整分析总结]] |

## 相关主题

- [[13-Lombok与DTO模式]]
- [[11-全局异常处理与统一响应]]
- [[MySQL/12-Java-Persistence/MyBatis-数据访问|MyBatis 数据访问]]

流量发来时, 按照以下顺序来处理:

```text

filter(清理请求, 请求不合法则拒绝服务): Power by tomcat
	|
interceptor(清理接口权限, 权限不足则拒绝服务): Power by Spring MVC
	|
controller(执行前,JSON -> DTO自动包装)
	|
service
	|
	...

```


