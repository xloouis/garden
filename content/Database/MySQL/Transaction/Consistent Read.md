---
tags:
    - mysql
    - transaction
title: 快照读
---

快照读

- [[InnoDB Isolation#可重复读 Transaction Isolation Levels Repeatable reads REPEATABLE READS|可重复读]] 和 [[InnoDB Isolation#读提交 Transaction Isolation Levels Read committed READ COMMITTED|读提交]] 下才能使用
- 常用的普通的SELECT语句在不加锁情况下就是快照读
    ```sql
    SELECT * FROM xx_table WHERE ...
    ```
- 即使用 [[MVCC]] 机制, 读取一个快照 ([[Undo Log]])

## See Also

- [[Locking Read]]
- [Consistent Nonlocking Reads](https://dev.mysql.com/doc/refman/8.4/en/innodb-consistent-read.html)
