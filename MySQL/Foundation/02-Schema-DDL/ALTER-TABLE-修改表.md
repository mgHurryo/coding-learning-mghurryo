---
title: ALTER TABLE 修改表
description: 面向初学者讲解如何添加,修改,重命名和删除列,索引及约束,并安全演进表结构.
tags:
  - MySQL
  - DDL
category: MySQL
mysql-version: "8.4 LTS"
---

# ALTER TABLE 修改表

`ALTER TABLE` 用于演进已有表的定义.它可以添加或删除列,修改类型和默认值,增加或删除索引与约束,以及重命名表.它不负责修改普通行数据;修改数据应使用 `UPDATE`.

> [!warning] 先读后改
> 先执行 `SHOW CREATE TABLE table_name`,保存原定义并确认连接环境.`ALTER TABLE` 成功后通常不能用普通事务回滚.

## 1. 添加列

```sql

ALTER TABLE articles
    ADD COLUMN summary VARCHAR(255) NULL
    AFTER title;
```

生产环境给已有大表新增 `NOT NULL` 列时,必须考虑旧行如何获得值.常见做法是先允许 `NULL` 或提供安全默认值,再由应用补齐数据,最后再收紧约束.

## 2. 修改列定义

`MODIFY COLUMN` 不改列名,只改类型或属性;改写时要把希望保留的属性完整写出:

```sql

ALTER TABLE articles
    MODIFY COLUMN title VARCHAR(240) NOT NULL
    COMMENT '文章标题';
```

如果省略原来的 `NOT NULL`,默认值,字符集或注释,可能无意中改变列行为.先用 `SHOW CREATE TABLE` 复制完整定义再编辑.

## 3. 重命名列

```sql

ALTER TABLE articles
    RENAME COLUMN body TO content;
```

`CHANGE COLUMN` 兼容更早版本,但必须同时重写完整新列定义:

```sql

ALTER TABLE articles
    CHANGE COLUMN content body LONGTEXT NOT NULL;
```

重命名会影响 SQL,ORM 映射,报表和下游任务.先部署兼容读路径,再切换写路径,最后清理旧引用.

## 4. 删除列和表名

```sql

ALTER TABLE articles DROP COLUMN summary;
ALTER TABLE articles RENAME TO blog_articles;
```

删除列会丢失该列数据,通常需要先备份或归档.若只是暂时停止使用,优先采用"停止写入 → 观察 → 归档 → 删除"的分阶段流程.

## 5. 添加和删除索引,约束

```sql

ALTER TABLE articles
    ADD CONSTRAINT uk_articles_slug UNIQUE (slug),
    ADD INDEX idx_articles_status_created (status, created_at);

ALTER TABLE articles
    DROP INDEX uk_articles_slug;
```

不同对象的删除语法不同:

```sql

ALTER TABLE orders DROP FOREIGN KEY fk_orders_user;
ALTER TABLE articles DROP CHECK chk_articles_status;
ALTER TABLE articles DROP PRIMARY KEY;
```

删除前用 `SHOW CREATE TABLE` 或 `SHOW INDEX` 确认真实名称.唯一约束通常以唯一索引形式存在,删除时使用 `DROP INDEX`.

## 6. 更改默认值和表选项

```sql

ALTER TABLE articles
    ALTER COLUMN status SET DEFAULT 'draft';

ALTER TABLE articles
    ALTER COLUMN status DROP DEFAULT;

ALTER TABLE articles
    ENGINE = InnoDB,
    COMMENT = '文章表';
```

修改字符集可能转换已有数据并重建索引:

```sql

ALTER TABLE articles
    CONVERT TO CHARACTER SET utf8mb4
    COLLATE utf8mb4_0900_ai_ci;
```

不要把"修改数据库默认字符集"和"转换已有表数据"混为一谈.

## 7. 一个可回滚思路的迁移例子

DDL 本身通常无法用 `ROLLBACK` 撤销,因此回滚应设计成反向迁移或恢复备份:

```sql

-- 阶段 1:兼容地增加新列
ALTER TABLE users ADD COLUMN display_name VARCHAR(100) NULL;

-- 应用双写并回填后,再收紧约束
ALTER TABLE users MODIFY COLUMN display_name VARCHAR(100) NOT NULL;
```

每一步都应有对应的前置检查,数据校验和反向操作,而不是把多个高风险动作塞在一条超长语句里.

## 8. 执行前检查

```sql

SELECT @@hostname, @@port, DATABASE(), CURRENT_USER();
SHOW CREATE TABLE articles;
SELECT COUNT(*) FROM articles;

-- 检查收紧为 NOT NULL 前是否有脏数据
SELECT COUNT(*) AS null_count
FROM articles
WHERE title IS NULL;
```

再评估表大小,索引,长事务,磁盘空间,复制延迟和应用版本.大表操作参考 [Online DDL](Online-DDL.md).

## 9. 常见错误

| 现象 | 处理思路 |
| :--- | :--- |
| `Duplicate entry` | 添加唯一键前清理重复数据并确定保留规则 |
| `Data truncated` | 先统计超长,非法或精度不足的数据 |
| `Cannot change column` | 检查外键,索引,生成列和依赖对象 |
| DDL 长时间等待 | 查询 MDL,长事务和阻塞会话,不要盲目重复提交 |
| 改完列后默认值消失 | `MODIFY COLUMN` 时遗漏原有属性 |
| 应用突然报列不存在 | 发布顺序不兼容;采用先加后用,后删策略 |

## 10. 相关主题

- [DDL 数据定义语言百科](DDL-数据定义语言百科.md)
- [CREATE TABLE 创建表](CREATE-TABLE-创建表.md)
- [Online DDL](Online-DDL.md)
- [SHOW INDEX 查看索引](SHOW-INDEX-查看索引.md)
- [锁等待排查 Lock Wait](锁等待排查-Lock-Wait.md)
- [数据迁移 Data Migration](数据迁移-Data-Migration.md)

## 11. 官方参考

- [MySQL 8.4 Reference Manual: ALTER TABLE Statement](https://dev.mysql.com/doc/refman/8.4/en/alter-table.html)
- [MySQL 8.4 Reference Manual: Online DDL](https://dev.mysql.com/doc/refman/8.4/en/innodb-online-ddl-operations.html)
