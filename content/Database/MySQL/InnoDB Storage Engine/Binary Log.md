---
title: binlog
tags:
    - mysql
---

- 在 InnoDB 之前就有, 所有引擎都能用
- 记录数据库表结构变更, 表数据修改 (DDL 和 DML)
- 用于数据备份/复制, 灾难恢复, 主从同步

---

## 数据格式

### statement

- SQL 语句的原文; 最早的一种, 现在不太使用
    ```sql
    # at 12345
    # statement: UPDATE employees SET name = "Hollis" WHERE id = 101;
    ```
- 如果使用了 uuid 或 now 等等动态函数, 会导致主从不一致

> [!warning] statement 格式下 MySQL 会禁用 READ COMMITTED 事务隔离级别
> 
> READ COMMITTED 下使用 statement 也可能导致主从不一致
> 
> [关于 MySQL 默认隔离级别的探讨](https://cloud.tencent.com/developer/article/1732590)

### row

- 记录每个数据更改的具体行的细节
- 不会导致不从不一致
- 缺点是比较大

### mixed

- statement + row, MySQL 自己看情况选择
- 在RR下, row 和 statement 都可以生效; 但是在RC下, 只有 row 格式才能生效

---

## binlog 刷盘

![[binlog-write.png]]

MySQL 给每个线程分配了一片内存用于缓冲 binlog, 叫 binlog cache

1. 事务执行过程中, 把日志写到 binlog cache (server 层的 cahce)
2. 事务提交时, 执行图里的 `write`, 把 binlog cache 写到 binlog 文件, 并清空 binlog cache; 此时并没有持久化到磁盘, 因为数据还缓存在文件系统的 page cache; 这个步骤不涉及磁盘I/O
3. 执行图里的 `fsync`, 涉及磁盘I/O, 将数据持久化到磁盘

MySQL提供一个 sync_binlog 参数来控制数据库的 binlog 刷到磁盘上的频率：

sync_binlog = 0 的时候，表示每次提交事务都只 write，不 fsync，后续交由操作系统决定何时将数据持久化到磁盘；

sync_binlog = 1 的时候，表示每次提交事务都会 write，然后马上执行 fsync；

sync_binlog =N(N>1) 的时候，表示每次提交事务都 write，但累积 N 个事务后才 fsync。

---

## Redo Log vs binlog

- binlog 所有引擎都能用, [[Redo Log]] InnoDB 能用
- binlog 追加写, 保全全量日志, 不会覆盖; Redo Log 是循环写
- binlog 用于备份恢复, 主从复制; Redo Log 用于掉电等故障恢复

## See Also

- [The Binary Log](https://dev.mysql.com/doc/refman/8.4/en/binary-log.html)
