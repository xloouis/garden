---
tags:
  - database
---
https://www.wikiwand.com/en/articles/ACID
## Atomicity
either _all_ occur, or _none_ occur
## Consistency (correctness)
In [database systems](https://www.wikiwand.com/en/articles/Database_systems "Database systems"), **consistency** (or **correctness**) refers to the requirement that any given [database transaction](https://www.wikiwand.com/en/articles/Database_transaction "Database transaction") must change affected data only in allowed ways. Any data written to the database must be valid according to all defined rules, including [constraints](https://www.wikiwand.com/en/articles/Integrity_constraints "Integrity constraints"), [cascades](https://www.wikiwand.com/en/articles/Cascading_rollback "Cascading rollback"), [triggers](https://www.wikiwand.com/en/articles/Database_trigger "Database trigger"), and any combination thereof.
## Isolation
[Isolation](https://www.wikiwand.com/en/articles/Isolation_\(database_systems\) "Isolation (database systems)") ensures that concurrent execution of transactions leaves the database in the same state that would have been obtained if the transactions were executed sequentially.
### See also
[[content/Database/Transaction Isolation Levels|Transaction Isolation Levels]]
## Durability
Guarantees that the effects of [transactions](https://www.wikiwand.com/en/articles/Database_transaction "Database transaction") that have been committed will survive permanently, even in cases of failures, including incidents and catastrophic events.
This usually means that completed transactions (or their effects) are recorded in [non-volatile memory](https://www.wikiwand.com/en/articles/Non-volatile_memory "Non-volatile memory").
