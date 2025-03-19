---
tags:
    - mysql
---

> [!info] Redo Log - Write Ahead Log (WAL) - 重做日志
> [MySQL 8.0: New Lock free, scalable WAL design](https://dev.mysql.com/blog-archive/mysql-8-0-new-lock-free-scalable-wal-design/)
> 
> [MySQL · 特性分析 · 8.0 对WAL的设计修改](http://mysql.taobao.org/monthly/2018/06/01/)

![[redo-log.png]]

---

- 只适用于 InnoDB
- 物理日志, 记录的是在某个数据页做了什么修改
- 保证了事务的[[InnoDB ACID#持久性（Durability）|持久性]]
- 事务提交时, 只需要持久化 Redo Log 就算完成了, 不需要等到 [[Buffer Pool]] 脏页刷盘
- 为了防止 [[Buffer Pool]] 脏页丢失设计的; 发生崩溃时, Buffer Pool 的脏页可能没持久化, 但是 Redo Log 持久化了; 重启后使用 Redo Log 恢复
- 使用追加操作, 磁盘操作是顺序写, 提升了性能

> [!info]- Redo Log 不是直接写入磁盘的, 通过 redo log buffer
> ![[redologbuf.webp]]

---

## Redo Log vs Undo Log

- 都只适用于 InnoDB
- [[Redo Log]] 记录事务**修改后**的数据状态, 记录的是更新后的值, 主要用于事务崩溃回复, 保证事务持久性
- [[Undo Log]] 记录事务**修改前**的数据状态, 记录的是更新前的值, 主要用于事务回滚, 保证事务的原子性
<br/>

- 事务提交失败/事务执行错误, MySQL重启后通过 Undo Log 回滚事务
- 事务提交后发生崩溃(宕机崩溃), 重启后通过 Redo Log 恢复事务

![[redo-vs-undo.png]]

## See Also

- [为什么需要 Redo Log](https://www.xiaolincoding.com/mysql/log/how_update.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81-redo-log) 
- [Redo Log](https://dev.mysql.com/doc/refman/8.4/en/innodb-redo-log.html)
