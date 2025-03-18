
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



