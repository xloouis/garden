---
tags:
  - mysql
  - lock
title: 记录锁
---

![[record-lock.png]]

记录锁 - Record Lock
## 特性

- [[Shared and Exclusive Locks]] 有共享锁&排它锁
- 行级锁
- 锁的是 "index records" ==索引记录==
- 如果一个索引也没有, 也会使用隐藏的聚簇索引 [`GEN_CLUST_INDEX`](https://dev.mysql.com/doc/refman/8.4/en/innodb-index-types.html)

```sql
-- 阻止对 c1=10 记录的增删改
SELECT c1 FROM t WHERE c1 = 10 FOR UPDATE;
```