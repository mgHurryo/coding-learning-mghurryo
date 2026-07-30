---
title: MySQL 数据约束 Constraint 百科
description: 面向初学者的 MySQL 数据约束语句手册，覆盖主键、非空、默认值、唯一、外键和 CHECK 约束。
tags:
  - MySQL
  - DDL
  - Constraint
category: MySQL
---

# MySQL 数据约束 Constraint 百科

## 一句话理解

数据约束（constraint）是写在数据库结构中的规则。它在 `INSERT`、`UPDATE` 和部分 `DELETE` 操作时自动检查数据，让“不合法的数据”在数据库入口处失败，而不是等应用代码事后补救。

约束解决的是**数据完整性**问题：

| 约束 | 要回答的问题 | 常见用途 |
| :--- | :--- | :--- |
| `PRIMARY KEY` | 这一行是谁？ | 行的唯一身份 |
| `NOT NULL` | 这个值能不能缺失？ | 必填字段 |
| `DEFAULT` | 没有传值时用什么？ | 初始状态、创建时间 |
| `UNIQUE` | 这个值能不能重复？ | 用户名、邮箱、订单号 |
| `FOREIGN KEY` | 引用的对象是否存在？ | 订单属于哪个用户 |
| `CHECK` | 值是否满足表达式？ | 年龄不能为负数 |

## 先看一个完整例子

```sql

CREATE TABLE users (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(255) NOT NULL,
    status TINYINT NOT NULL DEFAULT 1,
    age TINYINT UNSIGNED,
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    CONSTRAINT uk_users_username UNIQUE (username),
    CONSTRAINT uk_users_email UNIQUE (email),
    CONSTRAINT chk_users_age CHECK (age IS NULL OR age BETWEEN 0 AND 150)
) ENGINE = InnoDB;

CREATE TABLE orders (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    user_id BIGINT UNSIGNED NOT NULL,
    amount DECIMAL(10, 2) NOT NULL,
    PRIMARY KEY (id),
    CONSTRAINT fk_orders_user FOREIGN KEY (user_id) REFERENCES users (id)
) ENGINE = InnoDB;
```

这里的规则是：用户必须有用户名和邮箱；状态默认是 `1`；年龄可不填，但填了必须在 `0` 到 `150` 之间；订单必须指向已存在的用户。

## 1. `PRIMARY KEY` 主键约束

主键唯一标识一行记录。一个表只能有一个主键，但主键可以由多个列组成（联合主键）。主键列隐含 `NOT NULL`。

```sql

CREATE TABLE departments (
    id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL
);

-- 为已有表添加或删除主键
ALTER TABLE departments ADD PRIMARY KEY (id);
ALTER TABLE departments DROP PRIMARY KEY;
```

`AUTO_INCREMENT` 只是自动生成整数值，不等于主键；实际项目通常把它们一起使用。建主键前要先清理重复值和 `NULL`。

## 2. `NOT NULL` 非空约束

`NOT NULL` 表示列不能存放 SQL 的 `NULL`。它不等于“不能存空字符串 `''`”，也不等于“不能存数字 `0`”。

```sql

CREATE TABLE profiles (
    id BIGINT PRIMARY KEY,
    nickname VARCHAR(50) NOT NULL
);

ALTER TABLE profiles MODIFY nickname VARCHAR(50) NOT NULL;
ALTER TABLE profiles MODIFY nickname VARCHAR(50) NULL;
```

把列改成 `NOT NULL` 前，先检查 `SELECT * FROM profiles WHERE nickname IS NULL;`，否则变更可能失败。

## 3. `DEFAULT` 默认值约束

当 `INSERT` 没有提供该列时，MySQL 使用 `DEFAULT`。显式写入 `NULL` 通常不会触发默认值，若列又是 `NOT NULL` 则会报错。

```sql

CREATE TABLE tasks (
    id BIGINT PRIMARY KEY,
    status TINYINT NOT NULL DEFAULT 0,
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP
);

ALTER TABLE tasks ALTER COLUMN status SET DEFAULT 1;
ALTER TABLE tasks ALTER COLUMN status DROP DEFAULT;
```

默认值应表达稳定、明确的业务初始状态；复杂计算和依赖其他表的数据不适合放进默认值。

## 4. `UNIQUE` 唯一约束

`UNIQUE` 防止列或列组合出现重复值。它通常通过唯一索引实现，因此也会影响查询和写入性能。

```sql

CREATE TABLE accounts (
    id BIGINT PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    CONSTRAINT uk_accounts_email UNIQUE (email)
);

ALTER TABLE accounts ADD CONSTRAINT uk_accounts_email UNIQUE (email);
ALTER TABLE accounts DROP INDEX uk_accounts_email;
```

`NULL` 不是普通值：在常见的 MySQL 配置中，唯一索引允许多行 `NULL`。如果业务要求“最多一个空值”或“邮箱必填”，应配合 `NOT NULL` 或使用更明确的设计。

## 5. `FOREIGN KEY` 外键约束

外键要求子表的值能在父表的被引用列中找到。被引用列必须是主键或唯一键，相关列类型、符号属性也应保持一致。

```sql

CREATE TABLE order_items (
    id BIGINT PRIMARY KEY,
    order_id BIGINT UNSIGNED NOT NULL,
    CONSTRAINT fk_order_items_order
        FOREIGN KEY (order_id) REFERENCES orders (id)
        ON UPDATE CASCADE
        ON DELETE RESTRICT
);

ALTER TABLE order_items DROP FOREIGN KEY fk_order_items_order;
```

常见动作有：`RESTRICT` 拒绝会破坏引用的删除（通常是默认选择）、`CASCADE` 同步修改或删除、`SET NULL` 将子列置空（要求子列允许 `NULL`）。高并发或跨服务场景是否使用外键，要结合写入成本和一致性边界评估。

## 6. `CHECK` 检查约束

`CHECK` 要求表达式结果不能为假。MySQL 8.0.16 及之后版本会真正执行并强制检查；老版本可能只解析而不执行。

```sql

CREATE TABLE products (
    id BIGINT PRIMARY KEY,
    price DECIMAL(10, 2) NOT NULL,
    stock INT NOT NULL,
    CONSTRAINT chk_products_price CHECK (price >= 0),
    CONSTRAINT chk_products_stock CHECK (stock >= 0)
);

ALTER TABLE products DROP CHECK chk_products_price;
```

检查约束适合表达单行、简单且稳定的规则，不能用来查询其他表或替代复杂业务校验。注意：若表达式结果为 `UNKNOWN`（例如参与比较的值是 `NULL`），检查可能通过，因此常与 `NOT NULL` 配合。

## 7. 列级约束与表级约束

紧跟列定义写的是列级约束，适合单列规则；放在所有列定义之后的是表级约束，适合命名约束、联合唯一键、联合主键和外键。

```sql

CREATE TABLE memberships (
    user_id BIGINT NOT NULL,
    team_id BIGINT NOT NULL,
    role VARCHAR(20) NOT NULL DEFAULT 'member',
    PRIMARY KEY (user_id, team_id),
    CONSTRAINT chk_memberships_role CHECK (role IN ('member', 'admin'))
);
```

## 8. 修改、查看和删除约束

```sql

-- 查看建表语句（最完整）
SHOW CREATE TABLE users;

-- 查看列的可空性、默认值和键类型
DESCRIBE users;

-- 查看约束元数据
SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE
FROM information_schema.TABLE_CONSTRAINTS
WHERE TABLE_SCHEMA = DATABASE() AND TABLE_NAME = 'users';
```

删除前先确认约束名称。删除唯一约束通常使用 `DROP INDEX`；删除外键使用 `DROP FOREIGN KEY`；删除检查约束使用 `DROP CHECK`；删除主键使用 `DROP PRIMARY KEY`。

## 9. 新手常见报错排查

| 现象 | 优先检查 |
| :--- | :--- |
| `Duplicate entry` | 主键或唯一键已有相同值 |
| `Column ... cannot be null` | 插入了 `NULL`，或遗漏了 `NOT NULL` 列 |
| `Field ... doesn't have a default value` | 未传值且没有默认值 |
| `Cannot add or update a child row` | 外键值在父表不存在，或类型不一致 |
| `Check constraint ... is violated` | `CHECK` 表达式结果为假 |

## 10. 设计顺序与检查清单

1. 先确定每行的身份，通常设置一个短而稳定的主键。
2. 对业务上必填的列加 `NOT NULL`，明确 `NULL` 的业务含义。
3. 对稳定的初始状态设置 `DEFAULT`，不要用默认值掩盖缺失数据。
4. 对真正必须唯一的业务键加 `UNIQUE`，并考虑 `NULL` 行为。
5. 对单行范围规则加 `CHECK`；跨表关系再考虑 `FOREIGN KEY`。
6. 用 `SHOW CREATE TABLE` 验证最终结构，并在测试环境验证插入、更新、删除失败场景。

## 相关主题

- [CREATE TABLE 创建表](CREATE-TABLE-创建表.md)
- [ALTER TABLE 修改表](ALTER-TABLE-修改表.md)
- [PRIMARY KEY 主键约束](PRIMARY-KEY-主键约束.md)
- [UNIQUE 唯一约束](UNIQUE-唯一约束.md)
- [FOREIGN KEY 外键约束](FOREIGN-KEY-外键约束.md)
- [NOT NULL 非空约束](NOT-NULL-非空约束.md)
- [DEFAULT 默认值](DEFAULT-默认值.md)
