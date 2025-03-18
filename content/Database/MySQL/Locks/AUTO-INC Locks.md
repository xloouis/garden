---
title: 自增锁
tags:
  - mysql
  - lock
---
自增锁 - AUTO-INC Locks

一种特殊的表级锁，由插入带有 AUTO_INCREMENT 列的表的事务获取。在最简单的情况下，如果一个事务正在向表中插入值，任何其他事务都必须等待，以便执行它们自己的插入操作，这样第一个事务插入的行就会接收到连续的主键值。  
  
innodb_autoinc_lock_mode 变量控制用于自增锁定的算法。它允许你选择如何在可预测的自增值序列和插入操作的最大并发性之间进行权衡。

在早期的MySQL版本中（5.1之前），AUTO-INC锁是一个表级锁，它在第一个插入操作开始时被获取，并持续到整个事务结束。这意味着在同一时刻只有一个事务能对该表进行插入操作，这虽然保证了自增值的唯一性和连续性，但限制了并发性能。  
  
从MySQL 5.1开始，InnoDB引入了一种新的AUTO-INC锁策略，使得AUTO-INC锁在插入操作完成后立即释放，而不是在事务结束时释放。这种锁被称为“轻量级AUTO-INC锁”，它大大提高了并发插入的性能，因为不同的事务可以更快地连续插入新记录到同一个表中。

## See Also

- [AUTO_INCREMENT Handling in InnoDB](https://dev.mysql.com/doc/refman/8.4/en/innodb-auto-increment-handling.html)