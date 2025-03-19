---
title: 数据记录行格式
tag:
    - mysql
---

数据记录行格式

![[compact-mode-row-format.png]]

上图为 compact mode 的行格式

## 几个默认字段

- row_id: 隐式主键, 如果什么索引都不加, 会用这个字段来创建 [[Clustered and Secondary Indexes#聚簇索引 Clustered Indexes|聚簇索引]]
- trx_id: 对这条记录做了最新一次改动的==事务的ID==
- roll_ptr: 回滚指针, 指向这条记录的上一个版本, 也就是上一个版本的 [[Undo Log]] 日志

## 记录头

- delete_mask ：标识此条数据是否被删除。从这里可以知道，我们执行 detele 删除记录的时候，并不会真正的删除记录，只是将这个记录的 delete_mask 标记为 1, 最终由 purge 线程删除

- next_record：下一条记录的位置。从这里可以知道，记录与记录之间是通过链表组织的。在前面我也提到了，指向的是下一条记录的「记录头信息」和「真实数据」之间的位置，这样的好处是向左读就是记录头信息，向右读就是真实数据，比较方便。

- record_type：表示当前记录的类型，0表示普通记录，1表示B+树非叶子节点记录，2表示最小记录，3表示最大记录

## See Also

- [InnoDB 行格式有哪些](https://www.xiaolincoding.com/mysql/base/row_format.html#innodb-%E8%A1%8C%E6%A0%BC%E5%BC%8F%E6%9C%89%E5%93%AA%E4%BA%9B)
- [InnoDB Row Formats](https://dev.mysql.com/doc/refman/8.4/en/innodb-row-format.html#innodb-row-format-compact)
- [InnoDB Multi-Versioning](https://dev.mysql.com/doc/refman/8.4/en/innodb-multi-versioning.html)
