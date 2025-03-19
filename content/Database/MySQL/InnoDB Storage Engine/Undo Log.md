---
tags:
    - mysql
---

回滚日志 - Undo Log

- 只适用于 InnoDB
- 实现事务回滚, 或者崩溃时回滚(撤销)事务所作的修改, 保障了[[InnoDB ACID#原子性（Atomicity）|原子性]]
- Undo Log + [[Read View]] 实现 [[MVCC]]
- 会写到 [[Buffer Pool]] 中的 "undo页", 通过 [[Redo Log]] 持久化

---

一条记录的每一次更新操作产生的 undo log 格式都有一个 roll_pointer 指针和一个 trx_id 事务id：

- 通过 trx_id 可以知道该记录是被哪个事务修改的；
- 通过 roll_pointer 指针可以将这些 undo log 串成一个链表，这个链表就被称为版本链；

![[undolog-versions.png]]

---

![[Redo Log#Redo Log vs Undo Log]]

## See Also

- [Undo Logs](https://dev.mysql.com/doc/refman/8.4/en/innodb-undo-logs.html)
