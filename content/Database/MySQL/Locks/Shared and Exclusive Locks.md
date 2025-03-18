---
title: 共享锁&排它锁
tags:
  - mysql
  - lock
---
## 共享锁(读锁) [shared (`S`) lock](https://dev.mysql.com/doc/refman/8.4/en/glossary.html#glos_shared_lock "shared lock") 

读取操作创建的锁
- 允许多个事务同时读取数据 (锁兼容)
- 任何事务都不能对数据进行修改 (即获取排他锁), 直到已释放所有共享锁
## 排他锁 (写锁) [exclusive (`X`) lock](https://dev.mysql.com/doc/refman/8.4/en/glossary.html#glos_exclusive_lock "exclusive lock")

- 如果事务T对数据A加上排他锁后, 则其他事务不能再对A加任任何类型的锁
- 获得排他锁的事务既能读数据，又能修改数据。

```sql
# 共享锁 可以在 MySQL 5.7 和 MySQL 8.0 中使用
SELECT ... LOCK IN SHARE MODE;
# 共享锁 可以在 MySQL 8.0 中使用
SELECT ... FOR SHARE;
# 排他锁
SELECT ... FOR UPDATE;
```

