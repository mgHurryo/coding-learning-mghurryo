---
title: Spring 的一般项目结构
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