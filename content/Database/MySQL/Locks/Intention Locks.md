---
tags:
  - mysql
  - lock
title: 意向锁
---

意向锁 - Intention Locks

## 特性

- 表级锁
- 主要作用是表示当前表, 有事务加了行锁, 或者即将加行锁, ==可以快速判断表里是否有记录被加锁==
- 数据引擎自己维护的, 用户无法手动操作意向锁, 在为数据行加共享/排他锁之前, InnoDB 会先获取该数据行所在在数据表的对应意向锁
- 意向锁是数据库管理系统中用于实现锁协议的一种锁机制，旨在处理不同锁粒度（如行锁和表锁）之间的并发性问题。
- 意向锁并不是直接锁定资源，而是为了通知其他事务，以防止它们在资源上设置不兼容的锁。

## 意向共享锁 [intention shared lock](https://dev.mysql.com/doc/refman/8.4/en/glossary.html#glos_intention_shared_lock "intention shared lock") (`IS`)

事务有意向对表中的某些记录加共享锁（S 锁），加共享锁前必须先取得该表的 IS 锁。

```sql
SELECT ... FOR SHARE
```

## 意向排它锁 [intention exclusive lock](https://dev.mysql.com/doc/refman/8.4/en/glossary.html#glos_intention_exclusive_lock "intention exclusive lock") (`IX`)

事务有意向对表中的某些记录加排他锁（X 锁），加排他锁之前必须先取得该表的 IX 锁。

```sql
SELECT ... FOR UPDATE
```

## "表级锁"兼容性

> [!warning] 这里指的是表级别的, 意向锁不会与行级的共享锁和排他锁互斥

|      | `X`      | `IX`       | `S`        | `IS`       |
| ---- | -------- | ---------- | ---------- | ---------- |
| `X`  | Conflict | Conflict   | Conflict   | Conflict   |
| `IX` | Conflict | Compatible | Conflict   | Compatible |
| `S`  | Conflict | Conflict   | Compatible | Compatible |
| `IS` | Conflict | Compatible | Compatible | Compatible |
