---
title: 隔离级别
tags:
  - mysql
  - transaction
---

![[isolation-level.png]]

MySQL InnoDB 默认隔离级别是 [[Transaction Isolation Levels#Repeatable reads|可重复读]]

这四种隔离级别具体是如何实现的呢？

---

## 读未提交 [[Transaction Isolation Levels#Read uncommitted|READ UNCOMMITED]]

对于「读未提交」隔离级别的事务来说，因为可以读到未提交事务修改的数据，所以直接读取最新的数据就好了

## 读提交 [[Transaction Isolation Levels#Read committed|READ COMMITTED]]

每次读取数据, 都会生成一个新的 [[Read View]]

## 可重复读 [[Transaction Isolation Levels#Repeatable reads|REPEATABLE READS]]

启动事务时生成一个 [[Read View]], 然后整个事务期间都在用这个 Read View

## 串行化 [[Transaction Isolation Levels#Serializable|SERIALIZABLE]]

对于「串行化」隔离级别的事务来说，通过加读写锁的方式来避免并行访问

## 脏读/不可重复读/幻读解决

- [[Read Phenomena#Dirty reads|脏读]]

- [[Read Phenomena#Non-repeatable reads|不可重复读]]
    - 使用 [[MVCC]], 事务开始时生成 [[Read View]], 后面都用这一个

- [[Read Phenomena#Phantom reads|幻读]]
    - [[Consistent Read|快照读]](普通 select), 使用 [[MVCC]]
    - [[Locking Read|当前读]](select ... for update 等), 使用 [[Next-Key Locks]]
    - [幻读被完全解决了吗？](https://www.xiaolincoding.com/mysql/transaction/phantom.html#%E5%B9%BB%E8%AF%BB%E8%A2%AB%E5%AE%8C%E5%85%A8%E8%A7%A3%E5%86%B3%E4%BA%86%E5%90%97)
    - 但是，如果两个事务，事务1先进行快照读，然后事务2插入了一条记录并提交，再在事务1中进行update新插入的这条记录是可以更新成功的，这就是发生了幻读。  
    - 还有一种场景，如果两个事务，事务1先进行快照读，然后事务2插入了一条记录并提交，在事务1中进行了当前读之后，再进行快照读也会发生幻读。

## 事务相关命令

注意，执行「开始事务」命令，并不意味着启动了事务。在 MySQL 有两种开启事务的命令，分别是：

- 第一种：begin/start transaction 命令；
    - 执行了 begin/start transaction 命令后，并不代表事务启动了。只有在执行这个命令后，执行了第一条 select 语句，才是事务真正启动的时机；
- 第二种：start transaction with consistent snapshot 命令；
    - 执行了 start transaction with consistent snapshot 命令，就会马上启动事务。





