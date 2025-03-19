---
title: 🐬 MySQL
draft: true
---

[图解MySQL](https://www.xiaolincoding.com/mysql/)知识点学习笔记

----

## 基础篇

### 索引下推

联合索引, 只使用了部分字段, 用剩下的字段做判断跳过不满足条件的, 避免一次回表

执行计划显示 `Using index condition`

### MySQL的一行记录怎么存的

3个文件, `ibd` 独占表空间, `ibdata1` 共享表空间

按 "页" 为单位来读写

记录头信息-delete_mask: 执行delete的时候, 不会真的删除, 只是将 delete_mask 置为1

隐藏字段 row_id, trx_id, roll_ptr 和 mvcc 有关

#todo 行溢出 -> 多的数据存到溢出页 (保存溢出页的地址)

## 索引篇

### 常见面试题

索引的分类

按数据结构分类: b+tree, hash, full-text

`SELECT * FROM t_table WHERE a BETWEEN 2 AND 8 AND b = 2`

a BETWEEN 2 AND 8 类似与 >= 和 <=

综上所示，**联合索引的最左匹配原则，在遇到范围查询（如 >、<）的时候，就会停止匹配，也就是范围查询的字段可以用到联合索引，但是在范围查询字段的后面的字段无法用到联合索引。注意，对于 >=、<=、BETWEEN、like 前缀匹配的范围查询，并不会停止匹配，前面我也用了四个例子说明了**。

[联合索引](https://www.xiaolincoding.com/mysql/index/index_interview.html#%E6%8C%89%E5%AD%97%E6%AE%B5%E4%B8%AA%E6%95%B0%E5%88%86%E7%B1%BB)



