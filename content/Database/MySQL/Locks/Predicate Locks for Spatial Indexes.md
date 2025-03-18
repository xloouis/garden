---
tags:
  - mysql
  - lock
---
> [!quote]
> `InnoDB` supports `SPATIAL` indexing of columns containing spatial data (see [Section 13.4.9, “Optimizing Spatial Analysis”](https://dev.mysql.com/doc/refman/8.4/en/optimizing-spatial-analysis.html "13.4.9 Optimizing Spatial Analysis")).
> 
> To handle locking for operations involving `SPATIAL` indexes, next-key locking does not work well to support [`REPEATABLE READ`](https://dev.mysql.com/doc/refman/8.4/en/innodb-transaction-isolation-levels.html#isolevel_repeatable-read) or [`SERIALIZABLE`](https://dev.mysql.com/doc/refman/8.4/en/innodb-transaction-isolation-levels.html#isolevel_serializable) transaction isolation levels. There is no absolute ordering concept in multidimensional data, so it is not clear which is the “next” key.
> 
> To enable support of isolation levels for tables with `SPATIAL` indexes, `InnoDB` uses predicate locks. A `SPATIAL` index contains minimum bounding rectangle (MBR) values, so `InnoDB` enforces consistent read on the index by setting a predicate lock on the MBR value used for a query. Other transactions cannot insert or modify a row that would match the query condition.