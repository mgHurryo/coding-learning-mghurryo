---
title: CREATE DATABASE 创建数据库
description: 面向初学者讲解 MySQL 数据库的创建,字符集选择,验证方法和常见错误.
tags:
  - MySQL
  - DDL
category: MySQL
mysql-version: "8.4 LTS"
---

# CREATE DATABASE 创建数据库

`CREATE DATABASE` 用于创建数据库.在 MySQL 中,`DATABASE` 与 `SCHEMA` 基本同义;数据库主要是表,视图等对象的命名空间,并提供默认字符集和排序规则.

## 1. 最小语法

```sql
CREATE DATABASE learning_db;
```

创建后切换到该数据库:

```sql
USE learning_db;
```

`USE` 不是 DDL,它只改变当前会话后续语句默认使用的数据库.

## 2. 推荐写法

```sql

CREATE DATABASE IF NOT EXISTS learning_db
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_0900_ai_ci;
```

| 子句 | 含义 |
| :--- | :--- |
| `IF NOT EXISTS` | 同名数据库已存在时给出提示,而不是报错中断 |
| `CHARACTER SET utf8mb4` | 指定数据库默认字符集,可完整保存 Unicode 字符 |
| `COLLATE utf8mb4_0900_ai_ci` | 指定字符串比较与排序规则;`ai` 表示忽略重音,`ci` 表示忽略大小写 |

数据库级设置是新建表的默认值,不会强制所有表永远使用相同配置.表级或列级显式设置可以覆盖它.

## 3. 创建前后如何检查

```sql

-- 当前连接可见的数据库
SHOW DATABASES;

-- 查看真实创建语句
SHOW CREATE DATABASE learning_db;

-- 查看当前会话选择的数据库
SELECT DATABASE();

-- 查看默认字符集与排序规则
SELECT DEFAULT_CHARACTER_SET_NAME, DEFAULT_COLLATION_NAME
FROM information_schema.SCHEMATA
WHERE SCHEMA_NAME = 'learning_db';
```

初学者应养成"执行 DDL 后立即查看真实定义"的习惯.

## 4. 字符集和排序规则怎么选

新项目通常使用 `utf8mb4`.不要把 MySQL 的旧 `utf8mb3` 当成完整 UTF-8;它不能表示部分四字节字符.

排序规则决定字符串如何比较.例如,不区分大小写的排序规则可能认为 `Tom` 与 `tom` 相等,这会影响排序,查询条件和唯一索引.机器标识符若必须区分大小写,可选择合适的 `_as_cs` 或二进制排序规则,但应基于业务语义统一设计,详见 [Charset 字符集与排序规则](Charset-字符集与排序规则.md).

## 5. 修改数据库默认设置

```sql

ALTER DATABASE learning_db
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_0900_ai_ci;
```

这只改变数据库默认值,通常不会自动转换已有表和列.转换已有数据需要逐表评估 `ALTER TABLE ... CONVERT TO CHARACTER SET`,并检查索引长度,唯一性,乱码与锁表风险.

## 6. 权限与名称

创建数据库需要相应的 `CREATE` 权限.名称应简短,稳定,并避免保留字.名称含特殊字符时可用反引号,但更好的做法通常是采用简单命名:

```sql
CREATE DATABASE order_service;
```

Linux 上库名和表名的大小写行为可能与 Windows 不同.为提高可移植性,团队应统一使用小写命名,不依赖仅大小写不同的对象名.

## 7. 常见错误

| 现象 | 原因与处理 |
| :--- | :--- |
| `database exists` | 同名库已存在;先用 `SHOW CREATE DATABASE` 核对,不要盲目删除 |
| `Access denied` | 当前账号缺少 `CREATE` 权限,或没有服务器级创建许可 |
| 插入表情符号失败 | 库,表或连接可能没有统一使用 `utf8mb4` |
| 唯一值大小写行为不符合预期 | 排序规则与业务语义不一致 |
| 改了库字符集但旧表没变化 | `ALTER DATABASE` 只修改后续对象的默认值 |

## 8. 安全清单

- [ ] 已确认实例和环境,避免在错误服务器创建同名库.
- [ ] 已明确字符集和排序规则,而不是依赖服务器默认值.
- [ ] 已确认账号权限与后续备份,监控,授权方案.
- [ ] 已执行 `SHOW CREATE DATABASE` 验证结果.
- [ ] 初始化脚本使用版本控制,并能区分"创建"和"升级".

## 9. 相关主题

- [DDL 数据定义语言百科](DDL-数据定义语言百科.md)
- [Database 数据库概念](Database-数据库概念.md)
- [Charset 字符集与排序规则](Charset-字符集与排序规则.md)
- [CREATE TABLE 创建表](CREATE-TABLE-创建表.md)
- [DROP DATABASE 删除数据库](DROP-DATABASE-删除数据库.md)

## 10. 官方参考

- [MySQL 8.4 Reference Manual: CREATE DATABASE Statement](https://dev.mysql.com/doc/refman/8.4/en/create-database.html)
- [MySQL 8.4 Reference Manual: Charset General](https://dev.mysql.com/doc/refman/8.4/en/charset-general.html)
