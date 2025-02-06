---
tags:
  - database
  - transaction
aliases:
  - 读现象
---
https://www.wikiwand.com/en/articles/Isolation_(database_systems)#Read_Phenomena

When a transaction retrieves data that another transaction might have updated.
## Dirty reads [(Write-read conflict)](https://www.wikiwand.com/en/articles/Write-read_conflict)
_脏读_ | _无效数据的读出_ | _uncommitted dependency_

When a transaction retrieves a row that has been updated by another transaction that is not yet committed.
## Non-repeatable reads
_不可重复读_

When a transaction retrieves a row twice and that row is updated by another transaction that is committed in between.
## Phantom reads
_幻读_

When a transaction retrieves a set of rows twice and new rows are inserted into or removed from that set by another transaction that is committed in between.
## See also
[Multiversion concurrency control](https://www.wikiwand.com/en/articles/Multiversion_concurrency_control)
