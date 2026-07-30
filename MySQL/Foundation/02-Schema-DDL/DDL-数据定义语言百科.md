---
title: MySQL DDL 数据定义语言百科
description: 面向初学者的 MySQL DDL 总览,系统讲解数据库对象的创建,修改,删除,执行语义与安全实践.
tags:
  - MySQL
  - DDL
  - Encyclopedia
category: MySQL
mysql-version: "8.4 LTS"
---

# MySQL DDL 数据定义语言百科

DDL(Data Definition Language,数据定义语言)用于定义数据库的"结构".如果把数据库比作仓库,DML 负责搬入,查询和修改货物,DDL 负责修建仓库,增加货架,改变门的尺寸或拆除整个仓库.

> [!tip] 一句话记忆
> DDL 管结构,DML 管数据,DCL 管权限,TCL 管事务.

## 1. DDL 能操作什么

MySQL 中常见的数据库对象包括:

| 对象 | 作用 | 常见 DDL |
| :--- | :--- | :--- |
| Database / Schema | 容纳表,视图等对象的命名空间 | `CREATE DATABASE`,`ALTER DATABASE`,`DROP DATABASE` |
| Table | 保存数据的二维结构 | `CREATE TABLE`,`ALTER TABLE`,`RENAME TABLE`,`DROP TABLE`,`TRUNCATE TABLE` |
| Index | 加速查找或保证唯一性 | `CREATE INDEX`,`DROP INDEX`,也常写在 `ALTER TABLE` 中 |
| View | 保存一条查询定义 | `CREATE VIEW`,`ALTER VIEW`,`DROP VIEW` |
| Constraint | 限制可以写入的数据 | `PRIMARY KEY`,`UNIQUE`,`FOREIGN KEY`,`CHECK`,`NOT NULL`,`DEFAULT` |
| Trigger / Event | 在事件发生时自动执行逻辑 | `CREATE TRIGGER`,`CREATE EVENT` 等 |

日常学习应先掌握数据库,表,列和约束,再学习索引与 Online DDL.

## 2. 五组核心动作

| 动作 | 含义 | 典型示例 | 风险 |
| :--- | :--- | :--- | :--- |
| `CREATE` | 创建对象 | `CREATE TABLE` | 中等,需保证定义正确 |
| `ALTER` | 修改现有对象 | `ALTER TABLE ... ADD COLUMN` | 高,可能锁表或重建表 |
| `DROP` | 删除对象及其结构 | `DROP TABLE` | 极高,通常连同数据删除 |
| `TRUNCATE` | 快速清空整张表 | `TRUNCATE TABLE` | 极高,不能带 `WHERE` |
| `RENAME` | 修改对象名称 | `RENAME TABLE old TO new` | 中等,会影响应用引用 |

`DELETE FROM table_name` 属于 DML;`TRUNCATE TABLE table_name` 属于 DDL.二者都能清空数据,但执行语义,权限,触发器和自增计数器行为不同,详见 [TRUNCATE 与 DELETE 区别](TRUNCATE-与-DELETE-区别.md).

## 3. 第一个完整例子

下面从建库,选库,建表到验证结构,形成最小可执行流程:

```sql

CREATE DATABASE IF NOT EXISTS learning_db
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_0900_ai_ci;

USE learning_db;

CREATE TABLE users (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '用户 ID',
    username VARCHAR(50) NOT NULL COMMENT '登录名',
    status TINYINT NOT NULL DEFAULT 1 COMMENT '1=启用, 0=停用',
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    CONSTRAINT uk_users_username UNIQUE (username),
    CONSTRAINT chk_users_status CHECK (status IN (0, 1))
) ENGINE = InnoDB
  DEFAULT CHARACTER SET = utf8mb4
  COLLATE = utf8mb4_0900_ai_ci
  COMMENT = '用户表';

SHOW CREATE TABLE users;
DESCRIBE users;
```

阅读顺序:列名 → 数据类型 → 是否允许 `NULL` → 默认值 → 主键与其他约束 → 表选项.不要试图一次记住所有关键字,先理解每一段解决什么问题.

## 4. DDL 的重要执行语义

### 4.1 DDL 通常会隐式提交

多数 DDL 会在执行前后隐式提交当前事务.因此不要期望下面的 `ROLLBACK` 能撤销已经成功的建表或改表:

```sql

START TRANSACTION;
INSERT INTO audit_log(message) VALUES ('before ddl');
CREATE TABLE demo(id INT PRIMARY KEY);
ROLLBACK;
```

`CREATE TABLE` 可能已经使之前的事务提交.把 DDL 与普通业务 DML 分开执行,是更清晰的发布方式.

### 4.2 原子 DDL 不等于可以回滚

MySQL 8 的数据字典与 InnoDB 支持许多原子 DDL:服务器崩溃时,结构变更会整体成功或整体失败,避免只完成一半.这里的"原子"主要指崩溃一致性,不代表用户可以在成功后用 `ROLLBACK` 撤销.

### 4.3 所有 DDL 都涉及元数据锁

MySQL 使用 metadata lock(MDL)保护对象定义.一个长事务即使只是读过某张表,也可能让 `ALTER TABLE` 等待;排队中的 DDL 还可能阻塞后来访问该表的请求.所谓 Online DDL 也不是完全无锁,详见 [Online DDL](Online-DDL.md).

### 4.4 `IF EXISTS` 只能处理"对象不存在"

`IF EXISTS` 和 `IF NOT EXISTS` 适合让初始化脚本重复执行,但它们不会验证现有对象的结构是否符合预期:

```sql

CREATE TABLE IF NOT EXISTS users (id BIGINT PRIMARY KEY);
DROP TABLE IF EXISTS temporary_import;
```

如果 `users` 已存在但列定义错误,第一条语句通常只给出提示,不会自动修复结构.版本化迁移仍应显式记录每次变更.

## 5. 初学者推荐学习路线

1. 先理解 [Database 数据库概念](Database-数据库概念.md),[Table 表](Table-表.md),[Column 字段](Column-字段.md) 和 [DataType 数据类型](DataType-数据类型.md).
2. 用 [CREATE DATABASE 创建数据库](CREATE-DATABASE-创建数据库.md) 和 [CREATE TABLE 创建表](CREATE-TABLE-创建表.md) 建出一张最小表.
3. 学习 [数据约束 Constraint 百科](数据约束-Constraint-百科.md),让错误数据无法进入表.
4. 用 [ALTER TABLE 修改表](ALTER-TABLE-修改表.md) 演练新增列,修改列和删除列.
5. 最后理解 [Online DDL](Online-DDL.md) 以及生产环境的锁,耗时,兼容与回滚问题.
6. 把 [DROP TABLE 删除表](DROP-TABLE-删除表.md) 和 [DROP DATABASE 删除数据库](DROP-DATABASE-删除数据库.md) 当作高风险操作单独学习.

## 6. 查看现有结构

在修改对象前,先让数据库告诉你当前定义:

```sql

SHOW DATABASES;
SHOW TABLES;
SHOW CREATE DATABASE learning_db;
SHOW CREATE TABLE users;
DESCRIBE users;
SHOW INDEX FROM users;
```

`SHOW CREATE TABLE` 最接近真实 DDL,应优先用于复核字符集,排序规则,默认值,约束名,索引和表选项.

## 7. 命名与设计约定

- 库名,表名和列名使用稳定,明确的英文标识符,避免保留字和含义不清的缩写.
- 约束和索引显式命名,例如 `pk_users`,`uk_users_email`,`fk_orders_user`,`chk_products_price`.
- 每张 InnoDB 业务表通常设置一个短,稳定,不会变化的主键.
- 字符列明确字符集与排序规则;同一业务域尽量保持一致.
- 金额用 `DECIMAL`,时间点根据业务选择 `DATETIME` 或 `TIMESTAMP`,不要用字符串代替合适的数据类型.
- 注释解释单位,枚举值和业务含义,不要只把列名翻译一遍.

## 8. 生产变更检查清单

- [ ] 已确认连接的实例,环境,数据库和表名.
- [ ] 已用 `SHOW CREATE TABLE` 保存变更前定义.
- [ ] 已检查数据量,现有脏数据,依赖对象和应用兼容性.
- [ ] 已在相同 MySQL 版本与接近真实数据量的环境演练.
- [ ] 已评估 DDL 算法,MDL,磁盘空间,binlog 和复制延迟.
- [ ] 已设置合理的等待策略,并知道谁可能阻塞变更.
- [ ] 已准备备份,回滚或向前修复方案.
- [ ] 已在变更后复核结构,关键数据和应用指标.

## 9. 常见误区

| 误区 | 正确认识 |
| :--- | :--- |
| Online DDL 完全不锁表 | 开始和提交阶段仍需要 MDL,部分操作仍会重建或复制表 |
| DDL 放进事务就能回滚 | 多数 DDL 会隐式提交,成功后不能靠普通 `ROLLBACK` 撤销 |
| `IF NOT EXISTS` 会校验结构 | 它主要避免同名错误,不比较完整定义 |
| 加列一定很快 | 是否快速取决于版本,操作,算法,表定义和服务器条件 |
| 删除列只是隐藏字段 | 删除列会改变结构并可能丢失数据,恢复通常依赖备份 |
| 应用做了校验就不需要约束 | 并发,脚本和其他写入入口仍可能绕过应用校验 |

## 10. 条目导航

- 创建:[CREATE DATABASE 创建数据库](CREATE-DATABASE-创建数据库.md),[CREATE TABLE 创建表](CREATE-TABLE-创建表.md)
- 修改:[ALTER TABLE 修改表](ALTER-TABLE-修改表.md),[Online DDL](Online-DDL.md)
- 约束:[数据约束 Constraint 百科](数据约束-Constraint-百科.md),[PRIMARY KEY 主键约束](PRIMARY-KEY-主键约束.md),[NOT NULL 非空约束](NOT-NULL-非空约束.md),[DEFAULT 默认值](DEFAULT-默认值.md),[UNIQUE 唯一约束](UNIQUE-唯一约束.md),[FOREIGN KEY 外键约束](FOREIGN-KEY-外键约束.md),CHECK 检查约束
- 删除:[DROP TABLE 删除表](DROP-TABLE-删除表.md),[DROP DATABASE 删除数据库](DROP-DATABASE-删除数据库.md)

## 11. 官方参考

- [MySQL 8.4 Reference Manual: Data Definition Statements](https://dev.mysql.com/doc/refman/8.4/en/sql-data-definition-statements.html)
- [MySQL 8.4 Reference Manual: Atomic Data Definition Statement Support](https://dev.mysql.com/doc/refman/8.4/en/atomic-ddl.html)
- [MySQL 8.4 Reference Manual: Statements That Cause an Implicit Commit](https://dev.mysql.com/doc/refman/8.4/en/implicit-commit.html)
