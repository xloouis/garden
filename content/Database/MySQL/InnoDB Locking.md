---
draft: false
tags:
  - mysql
  - lock
---

## 加什么锁

参考资料:
- [Locks Set by Different SQL Statements in InnoDB](https://dev.mysql.com/doc/refman/8.4/en/innodb-locks-set.html)
- [⭐️ MySQL是怎么加锁的](https://www.xiaolincoding.com/mysql/lock/how_to_lock.html#%E6%80%BB%E7%BB%93)

不同事务隔离级别下
- 读未提交/读已提交
    - 只有 [[Record Locks]] 记录锁
- 可重复读
    - [[Record Locks]]
    - [[Gap Locks]]
    - [[Next-Key Locks]]

----

> [!warning] 聚簇/非聚簇索引上都有可能加锁

- 原则
    1. 加锁的基本单位是 [[Next-Key Locks]], 是一个前开后闭区间。  
    2. 查找过程中访问到的对象才会加锁。  
- 优化
    1. 索引上的等值查询，给唯一索引加锁的时候，[[Next-Key Locks]] 退化为[[Record Locks]] 记录锁。  
    2. 索引上的等值查询，向右遍历时且最后一个值不满足等值条件的时候，[[Next-Key Locks]] 退化为[[Gap Locks]] 间隙锁。  
- 一个 bug: 唯一索引上的范围查询会访问到不满足条件的第一个值为止。

> [!example]
> 比如一个表 id 为主键, 有如下数据
> ```text
> 0 5 10 15 20 25
> ```
> 执行 `select * from t where id>10 and id<=15 for update`
> 
> 根据原则 1，加锁单位是 next-key lock，会给 (10,15]加上 next-key lock，并且因为 id 是唯一键，所以循环判断到 id=15 这一行就应该停止了。  
> 
> 但是，InnoDB 会往前扫描到第一个不满足条件的行为止，也就是 id=20。而且由于这是个范围扫描，因此索引 id 上的 (15,20]这个 next-key lock 也会被锁上。

## [update 没加索引会走全表?](https://xiaolincoding.com/mysql/lock/update_index.html#为什么会发生这种的事故)

在 update 语句的 where 条件没有使用索引，就会全表扫描，于是就会对所有记录加上 next-key 锁（记录锁 + 间隙锁），相当于把整个表锁住了

## 锁分类

### 全局锁

 `FLUSH TABLES WITH READ LOCK`
 
### 表级锁

- [[Intention Locks]] 意向锁
- [[AUTO-INC Locks]] 自增锁
- [[Metadata Locking]] 字典锁
- 普通表级锁
    - `LOCK TABLES table READ`
    - `LOCK TABLES table WRITE`

### 行锁

- [[Record Locks]] 记录锁, 只锁一行
- 带间隙 Gap 的锁
    - [[Gap Locks]] 间隙锁
    - [[Next-Key Locks]] 临键锁
    - [[Insert Intention Locks]] 插入意向锁

### 其他

- [[Shared and Exclusive Locks]] 共享锁&排它锁
- [[Predicate Locks for Spatial Indexes]]


## See also

- [InnoDB Locking](https://dev.mysql.com/doc/refman/8.4/en/innodb-locking.html)
- [如果查询条件没有索引字段的话，是加「行锁」还是加「表锁」？](https://www.51cto.com/article/742560.html)
- [MySql实战45讲](https://jums.gitbook.io/mysql-shi-zhan-45-jiang)
