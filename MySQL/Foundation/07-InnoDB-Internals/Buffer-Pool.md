---
title: Buffer Pool
description: 说明 InnoDB Buffer Pool 的数据页缓存、脏页、LRU 和调优关注点。
tags:
  - MySQL
  - InnoDB
  - Buffer-Pool
category: MySQL
---

# Buffer Pool

## 速览

Buffer Pool 是 InnoDB 最重要的内存区域，用来缓存数据页和索引页。命中 Buffer Pool 的查询通常避免磁盘 I/O，写入也会先修改内存页形成脏页。

## 核心机制

InnoDB 以页为单位读写数据。查询页不在内存时要从磁盘读入；更新页时先改 Buffer Pool 中的页，同时记录 redo log，之后再异步刷脏页。Buffer Pool 使用改进 LRU，避免全表扫描把热点页全部冲掉。

## SQL/配置示例

```sql
SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
SHOW GLOBAL STATUS LIKE 'Innodb_buffer_pool_read%';
```

## 项目落地

热点业务表和索引应尽量让工作集留在 Buffer Pool。索引过多、字段过宽、全表扫描和大批量任务都会增加缓存压力。

## 常见错误

不要只看磁盘容量，不看 Buffer Pool 命中率；不要让报表类全表扫描和在线接口共享同一个高峰资源窗口。

## 面试追问

- Buffer Pool 缓存的是什么？
- 脏页什么时候刷盘？
- 为什么大查询可能影响在线接口？

## 排障/边界

读 I/O 飙升时检查 Buffer Pool 命中、慢 SQL、全表扫描和索引失效。写入抖动时关注脏页比例、checkpoint 和 redo 压力。

## 相关主题

- [checkpoint](checkpoint.md)
- [redo log](redo-log.md)
- [慢 SQL 排查流程](慢-SQL-排查流程.md)

## 参考资料

- [MySQL 8.4 Reference Manual - The InnoDB Buffer Pool](https://dev.mysql.com/doc/refman/8.4/en/innodb-buffer-pool.html)
