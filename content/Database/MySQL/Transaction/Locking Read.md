---
tags:
    - mysql
    - transaction
title: 当前读
---

当前读

加锁的 SELECT，或者对数据进行增删改都会进行当前读

```sql
SELECT * FROM xx_table LOCK IN SHARE MODE;

SELECT * FROM xx_table FOR UPDATE;

INSERT INTO xx_table ...

DELETE FROM xx_table ...

UPDATE xx_table ...
```

___

实现悲观锁 #todo 

## See Also

- [[Consistent Read]]
- [Locking Reads](https://dev.mysql.com/doc/refman/8.4/en/innodb-locking-reads.html)