---
tags:
    - mysql
    - transaction
---

![[readview结构.drawio.png]]

数据快照, 作用: 帮我们解决可见性问题, 告诉我们本次事务内应该能看到哪些快照

---

Read View 有四个重要的字段：

- **creator_trx_id** ：指的是创建该 Read View 的事务的事务 id。

- **m_ids** ：指的是在创建 Read View 时，当前数据库中「活跃事务」的事务 id 列表，注意是一个列表，“活跃事务”指的就是，==启动了但还没提交的事务==。
    - m_ids 范围满足 [min_trx_id, max_trx_id)

- **min_trx_id** ：指的是在创建 Read View 时，当前数据库中「活跃事务」中事务 id 最小的事务，也就是 ==m_ids 的最小值==。

- **max_trx_id** ：这个并不是 m_ids 的最大值，而是创建 Read View 时当前数据库中应该给下一个事务的 id 值，也就是全局事务中最大的事务 id 值 + 1；

## 判断可见性

一个事务, 能看到的是在他开始之前就已经提交的事务的结果，而未提交的结果都是不可见的

#todo 简单来说, 先找到数据记录里的 `trx_id`, 然后根据 trx_id 的单调递增性和 Read View 里记录的活跃事务id列表, 判断这条记录可不可见

> [!warning] [[Row Format|数据行]] 只有[[Clustered and Secondary Indexes|聚簇索引]]能直接取到 #todo 

- 可重复读/读提交级别下, 生成了 Read View
- 查找到[[Row Format|数据行]]的 `trx_id`
<br/>

- `trx_id = creator_trx_id`, 可见, 自己本事务内的操作的记录
- `trx_id < min_trx_id`, 在本 Read View 之前的记录, 可见
- `trx_id >= max_trx_id`, 在本 Read View 之后的记录, 不可见
- `min_trx_id <= trx_id < max_trx_id`
    - `trx_id` in `m_ids`, 该事务还未提交, 不可见
    - `trx_id` not in `m_ids`, 事务已经被提交, 可见

那不可见的话，undolog #todo 


## See Also

- [Read View 在 MVCC 里如何工作的](https://www.xiaolincoding.com/mysql/transaction/mvcc.html#read-view-%E5%9C%A8-mvcc-%E9%87%8C%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84)
