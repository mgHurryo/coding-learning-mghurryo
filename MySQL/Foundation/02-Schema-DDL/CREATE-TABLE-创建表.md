---
title: CREATE TABLE 创建表
description: 面向初学者讲解如何定义 MySQL 表,列,数据类型,约束,索引和表选项.
tags:
  - MySQL
  - DDL
category: MySQL
mysql-version: "8.4 LTS"
---

# CREATE TABLE 创建表

`CREATE TABLE` 用一条 DDL 语句定义表的名称,列,约束,索引和存储选项.表定义决定了以后什么数据能写入,如何查找,以及结构变更的成本.

## 1. 最小语法

```sql
CREATE TABLE notes (
    id BIGINT PRIMARY KEY,
    title VARCHAR(200) NOT NULL
);
```

每个列定义至少包含:列名和数据类型;之后按需要添加 `NOT NULL`,`DEFAULT`,`AUTO_INCREMENT`,`COMMENT` 等属性.列之间用逗号分隔,最后一个定义不要多写逗号.

## 2. 推荐的完整结构

```sql

CREATE TABLE articles (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '文章 ID',
    author_id BIGINT UNSIGNED NOT NULL COMMENT '作者 ID',
    slug VARCHAR(120) NOT NULL COMMENT 'URL 标识',
    title VARCHAR(200) NOT NULL,
    body TEXT NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'draft',
    published_at DATETIME NULL,
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP
        ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    CONSTRAINT uk_articles_slug UNIQUE (slug),
    CONSTRAINT chk_articles_status
        CHECK (status IN ('draft', 'published', 'archived')),
    INDEX idx_articles_author_status (author_id, status)
) ENGINE = InnoDB
  DEFAULT CHARACTER SET = utf8mb4
  COLLATE = utf8mb4_0900_ai_ci
  COMMENT = '文章表';
```

### 2.1 列定义的阅读方式

| 片段 | 含义 |
| :--- | :--- |
| `BIGINT UNSIGNED` | 无符号的大整数,适合不断增长的 ID |
| `NOT NULL` | 不允许 SQL `NULL` |
| `AUTO_INCREMENT` | 插入时自动生成递增值;它本身不是约束身份的主键 |
| `DEFAULT` | `INSERT` 省略列时使用的值 |
| `COMMENT` | 给维护者看的说明,不会自动校验业务 |
| `ON UPDATE` | 行被更新时自动刷新时间,需确认是否符合审计语义 |

### 2.2 约束和索引

主键,唯一键,外键,检查约束和普通索引可以写在列定义后.表级写法更适合命名约束,联合键和跨列规则,详见 [数据约束 Constraint 百科](数据约束-Constraint-百科.md).

## 3. 常用数据类型选择

- 整数 ID:优先选择能覆盖增长范围的 `INT` 或 `BIGINT`,需要比较两侧的有无符号属性.
- 文本:短且需要索引时用 `VARCHAR`;正文或大文本用 `TEXT`,不要把长度不受控的正文放进列表查询.
- 金额:使用 `DECIMAL(precision, scale)`,不要用 `FLOAT` 表示需要精确小数的金额.
- 时间:根据时区和范围选择 `DATETIME` 或 `TIMESTAMP`,并统一应用层约定.
- JSON:适合结构变化但仍属于一条记录的属性;高频筛选字段应考虑生成列或独立列索引.

完整类型说明见 [DataType 数据类型](DataType-数据类型.md).

## 4. 表名,列名和保留字

名称尽量使用小写,稳定的英文和下划线,避免 `order`,`group` 等保留字.反引号只能暂时解决名称冲突,不能替代良好命名:

```sql

CREATE TABLE `order` (id BIGINT PRIMARY KEY);
```

跨平台部署时不要依赖大小写差异;Linux 与 Windows 的表名大小写行为可能不同.

## 5. `IF NOT EXISTS` 与验证

```sql

CREATE TABLE IF NOT EXISTS import_buffer (
    id BIGINT PRIMARY KEY,
    payload JSON NOT NULL
);

SHOW CREATE TABLE import_buffer;
DESCRIBE import_buffer;
SHOW INDEX FROM import_buffer;
```

`IF NOT EXISTS` 只避免同名对象错误,不会把已有表改成新定义.版本化迁移应明确写出 `ALTER TABLE`,并在部署后再次执行 `SHOW CREATE TABLE`.

## 6. 建表前后的设计顺序

1. 写出每行代表什么,确定主键和唯一业务标识.
2. 列出字段及其数据类型,单位,可空性和默认值.
3. 添加跨列约束,外键和常用访问路径的索引.
4. 选择字符集,排序规则,存储引擎并写入注释.
5. 在测试库插入合法,边界和非法数据,验证失败是否符合预期.

## 7. 常见错误

| 错误 | 影响 | 改进 |
| :--- | :--- | :--- |
| 没有主键 | 更新定位,复制和索引组织变差 | 通常为 InnoDB 表设置稳定主键 |
| 所有列都允许 `NULL` | "未知"和"空值"混在一起 | 为业务必填列使用 `NOT NULL` |
| 用字符串保存数字或时间 | 排序,比较和索引行为异常 | 选用语义正确的数据类型 |
| 主键使用过长字符串 | 所有二级索引变宽 | 选择短且稳定的整数或紧凑键 |
| 联合索引顺序随意 | 查询无法有效使用索引 | 按过滤,排序和选择性设计并用 `EXPLAIN` 验证 |
| 直接把生产建表脚本重复执行 | 结构不一定一致,且可能失败 | 使用迁移工具并记录版本 |

## 8. 相关主题

- [DDL 数据定义语言百科](DDL-数据定义语言百科.md)
- [Column 字段](Column-字段.md)
- [DataType 数据类型](DataType-数据类型.md)
- [数据约束 Constraint 百科](数据约束-Constraint-百科.md)
- [ALTER TABLE 修改表](ALTER-TABLE-修改表.md)
- [Index 索引概念](Index-索引概念.md)

## 9. 官方参考

- [MySQL 8.4 Reference Manual: CREATE TABLE Statement](https://dev.mysql.com/doc/refman/8.4/en/create-table.html)
