---
tags:
  - mysql
  - lock
title: 间隙锁
---

![[gap-lock.png]]

间隙锁 - Gap Lock

- 在索引记录之间的间隙上的锁, 或者在第一个索引记录之前或最后一个索引记录之后的间隙上的锁。
- 不包含记录本身
- Gap指的是InnoDB的索引数据结构中可以插入新值的位置。

- 只在 `Repeatable Reads` 下生效
- 对于具有唯一搜索条件的唯一索引, InnoDB只锁定找到的索引记录, 而不会锁定间隙
- 对于其他搜索条件, InnoDB锁定扫描的索引范围, 使用 gap lock 或 next-key lock 来阻塞其他事务插入范围覆盖的间隙
    ```sql
    -- 如果id没有索引, 或者该索引不唯一, 会锁定间隙
    SELECT * FROM child WHERE id = 100;
    ```
- 共享的, 多个事务可以同时持有同一个 gap 的间隙锁, 不存在互斥关系, 因为间隙锁的目的是为了防止幻读
