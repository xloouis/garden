---
tags:
  - mysql
  - transaction
---

多版本并发控制 - Multiversion Concurrency Control

- [[Read View]] + [[Undo Log]] 实现
- Undo Log 中保存了历史快照, 通过 roll_pointer 指针形成版本链
- Read View 用来判断具体的一个快照是否可见

> [!warning] trx_id 和 roll_pointer 为 [[Row Format]] 中的字段
> 版本链头结点(即当前记录行), 只存在于聚簇索引, 非聚簇索引没有
> 
> [[#发生索引覆盖, MVCC 怎么使用?]]

![[mvcc-workflow.svg]]

---

## 发生索引覆盖, MVCC 怎么使用?

索引覆盖, 即只用到了二级索引(非聚簇索引), 不需要回表, 就查不到聚簇索引中的 trx_id 和 roll_pointer?

> [!quote] [Optimizing InnoDB Transaction Management](https://dev.mysql.com/doc/refman/8.4/en/optimizing-innodb-transaction-management.html)
> If secondary index pages are found to have a `PAGE_MAX_TRX_ID` that is too new, or if records in the secondary index are delete-marked, `InnoDB` may need to look up records using a clustered index.
> 
> 如果二级索引页的 `PAGE_MAX_TRX_ID` 太新, 或者记录被标记了删除标记, InnoDB 可能会使用聚簇索引查询记录
>
> `page_max_trx_id` 表示改过该页的最大事务id (在数据页的页头 Page Header 里, 仅在二级索引中定义)

> [!quote] [Multi-Versioning and Secondary Indexes](https://dev.mysql.com/doc/refman/8.0/en/innodb-multi-versioning.html)
> `InnoDB` multiversion concurrency control (MVCC) treats secondary indexes differently than clustered indexes. Records in a clustered index are updated in-place, and their hidden system columns point undo log entries from which earlier versions of records can be reconstructed. Unlike clustered index records, secondary index records do not contain hidden system columns nor are they updated in-place.
> 
> When a secondary index column is updated, old secondary index records are delete-marked, new records are inserted, and delete-marked records are eventually purged. ==When a secondary index record is delete-marked or the secondary index page is updated by a newer transaction, `InnoDB` looks up the database record in the clustered index.== In the clustered index, the record's `DB_TRX_ID` is checked, and the correct version of the record is retrieved from the undo log if the record was modified after the reading transaction was initiated.
> 
> If a secondary index record is marked for deletion or the secondary index page is updated by a newer transaction, the [covering index](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_covering_index "covering index") technique is not used. Instead of returning values from the index structure, `InnoDB` looks up the record in the clustered index.
> 
> However, if the [index condition pushdown (ICP)](https://dev.mysql.com/doc/refman/8.0/en/index-condition-pushdown-optimization.html "10.2.1.6 Index Condition Pushdown Optimization") optimization is enabled, and parts of the `WHERE` condition can be evaluated using only fields from the index, the MySQL server still pushes this part of the `WHERE` condition down to the storage engine where it is evaluated using the index. If no matching records are found, the clustered index lookup is avoided. If matching records are found, even among delete-marked records, `InnoDB` looks up the record in the clustered index.

- 如果 [[Read View]] 的 min_trx_id > `page_max_trx_id` (索引页未被之后的事务更新过), 并且记录未被删除, 说明这个二级索引页的记录可见, 可以走索引覆盖
- 否则不使用索引覆盖, 回表, 使用聚簇索引, 继续用 MVCC (二级索引的索引下推技术还是会用的)


## See Also

- [InnoDB Multi-Versioning](https://dev.mysql.com/doc/refman/8.4/en/innodb-multi-versioning.html)