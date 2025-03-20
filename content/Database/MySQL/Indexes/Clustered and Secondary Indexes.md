---
title: 聚簇索引/二级索引
tags:
    - mysql
    - index
---

## 数据结构

> [!warning]
> [【驳斥八股文系列】别瞎分析了，MongoDB 使用的是 B+ 树，不是你们以为的 B 树](https://zhuanlan.zhihu.com/p/519658576)

[[B+ tree]]

---

## 聚簇索引 Clustered Index

- 数据和索引放在一起
- 叶子节点存储的是整行数据

规则:
- 先找主键索引
- 再找第一个 unique 索引
- 都找不到使用默认字段 row_id 创建索引 GEN_CLUST_INDEX

---

## 二级索引 Secondary Index (非聚簇)

- 数据和索引分开放
- 叶子节点包含索引值和指向数据页数据行的逻辑指针

## See Also

- [Clustered and Secondary Indexes](https://dev.mysql.com/doc/refman/8.4/en/innodb-index-types.html)
