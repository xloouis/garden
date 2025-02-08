---
tags:
  - mysql
  - innodb
  - transaction
  - database
draft: true
---
## REPEATABLE READ
default isolation level

[Consistent reads](https://dev.mysql.com/doc/refman/8.4/en/glossary.html#glos_consistent_read "consistent read") within the same transaction read the [snapshot](https://dev.mysql.com/doc/refman/8.4/en/glossary.html#glos_snapshot "snapshot") established by the first read.

For [locking reads](https://dev.mysql.com/doc/refman/8.4/en/glossary.html#glos_locking_read "locking read") ([`SELECT`](https://dev.mysql.com/doc/refman/8.4/en/select.html "15.2.13 SELECT Statement") with `FOR UPDATE` or `FOR SHARE`), [`UPDATE`](https://dev.mysql.com/doc/refman/8.4/en/update.html "15.2.17 UPDATE Statement"), and [`DELETE`](https://dev.mysql.com/doc/refman/8.4/en/delete.html "15.2.2 DELETE Statement") statements
- For a unique index with a unique search condition, `InnoDB` locks only the index record found, not the [gap](https://dev.mysql.com/doc/refman/8.4/en/glossary.html#glos_gap "gap") before it.
- For other search conditions, `InnoDB` locks the index range scanned, using [gap locks](https://dev.mysql.com/doc/refman/8.4/en/glossary.html#glos_gap_lock "gap lock") or [next-key locks](https://dev.mysql.com/doc/refman/8.4/en/glossary.html#glos_next_key_lock "next-key lock") to block insertions by other sessions into the gaps covered by the range. For information about gap locks and next-key locks, see [Section 17.7.1, “InnoDB Locking”](https://dev.mysql.com/doc/refman/8.4/en/innodb-locking.html "17.7.1 InnoDB Locking").
## READ COMMITTED
Each consistent read, even within the same transaction, sets and reads its own fresh snapshot.

For locking reads ([`SELECT`](https://dev.mysql.com/doc/refman/8.4/en/select.html "15.2.13 SELECT Statement") with `FOR UPDATE` or `FOR SHARE`), [`UPDATE`](https://dev.mysql.com/doc/refman/8.4/en/update.html "15.2.17 UPDATE Statement") statements, and [`DELETE`](https://dev.mysql.com/doc/refman/8.4/en/delete.html "15.2.2 DELETE Statement") statements, `InnoDB` locks only index records, not the gaps before them, and thus permits the free insertion of new records next to locked records.

todo
InnoDB 通过使用 MVCC 来解决不可重复读的问题。在RR这种隔离级别下，当我们使用快照读进行数据读取的时候，只会在第一次读取的时候生成一个Read View，后续的所有快照读都是用的同一个快照，所以就不会发生不可重复读的问题了。
## See also
- https://dev.mysql.com/doc/refman/8.4/en/innodb-transaction-isolation-levels.html
- https://dev.mysql.com/doc/refman/8.4/en/innodb-consistent-read.html
- https://dev.mysql.com/doc/refman/8.4/en/innodb-next-key-locking.html