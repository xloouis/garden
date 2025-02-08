---
tags:
  - database
  - transaction
draft: true
---
## Isolation levels vs read phenomena
|                  | Dirty read | Non-repeatable read | Phantom read |
| ---------------- | ---------- | ------------------- | ------------ |
| Serializable     | no         | no                  | no           |
| Repeatable read  | no         | no                  | yes          |
| Read committed   | no         | yes                 | yes          |
| Read uncommitted | yes        | yes                 | yes          |
## Prevent non-repeatable reads and phantom reads
- lock-based
- Multiversion concurrency control
	non-repeatable reads and phantom reads may occur when the requirement that a transaction affected by a commit conflict must be rolled back is relaxed.
## Serializable
可串行化
==Highest==
### Lock-based
- read and write locks (acquired on selected data)
- ==rang-locks== when a `SELECT` query uses a ranged _WHERE_ clause, especially to avoid the [[Read Phenomena#Phantom reads|phantom reads]] phenomenon.
### Non-lock based
- no locks
- [Snapshot isolation](https://www.wikiwand.com/en/articles/Snapshot_isolation) implemented within [Multiversion concurrency control](https://www.wikiwand.com/en/articles/Multiversion_concurrency_control)
## Repeatable reads
可重复读

Write skew is possible at this isolation level in some systems. Write skew is a phenomenon where two writes are allowed to the same column(s) in a table by two different writers (who have previously read the columns they are updating), resulting in the column having data that is a mix of the two transactions.
### Lock-based
- read and write locks
- _range-locks_ are not managed, so [[Read Phenomena#Phantom reads|phantom reads]] can occur
## Read committed
提交读 | 读已提交
guarantees that any data read is committed at the moment it is read. It simply restricts the reader from seeing any intermediate, uncommitted, 'dirty' read.
### Lock-based
- write locks
- read locks are released as soon as the SELECT operation is performed (so the [[Read Phenomena#Non-repeatable reads|non-repeatable]] can occur in this isolation level)
- _range-locks_ are not managed
## Read uncommitted
未提交读
==lowest==
[[Read Phenomena#Dirty reads [(Write-read conflict)](https //www.wikiwand.com/en/articles/Write-read_conflict)|dirty reads]] are allowed
## See also
[Isolation levels](https://www.wikiwand.com/en/articles/Isolation_(database_systems)#Isolation_levels)
