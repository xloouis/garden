---
tags:
    - mysql
---

基于内存, 为了提升读写性能, 避免直接和磁盘交互, 引入了 Buffer Pool

![[buffer-pool.png]]

![[redo-log.png]]

---

- Buffer Pool ==按页为单位==来缓存, Buffer Pool 中的页叫做缓存页
- 当读取数据时，如果数据存在于 Buffer Pool 中，客户端就会直接读取 Buffer Pool 中的数据，否则再去磁盘中读取。
- 当修改数据时，如果数据存在于 Buffer Pool 中，那直接修改 Buffer Pool 中数据所在的页，然后将其页设置为脏页（该页的内存数据和磁盘上的数据已经不一致），为了减少磁盘I/O，不会立即将脏页写入磁盘，后续由后台线程选择一个合适的时机将脏页写入到磁盘。(脏页刷盘)

> [!info] undo 页记录什么
> 开启事务后，InnoDB 层更新记录前，首先要记录相应的 [[Undo Log]]，如果是更新操作，需要把被更新的列的旧值记下来，也就是要生成一条 undo log，undo log 会写入 Buffer Pool 中的 undo 页面。

## See Also

- [Buffer Pool](https://dev.mysql.com/doc/refman/8.4/en/innodb-buffer-pool.html)
- [Configuring Buffer Pool Flushing](https://dev.mysql.com/doc/refman/8.4/en/innodb-buffer-pool-flushing.html)
