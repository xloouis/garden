---
draft: false
title: SQL 优化
tags:
  - mysql
---

> [!quote] SQL 优化方法有哪些
> 1. 硬件/配置参数(各种buffer大小)
> 2. 索引命中
> 3. 减少 I/O, 减少回表
> 4. 减少数据量(网络传输)

#todo 

- 使用 `EXPLAIN`
- 避免[[Index Invalidation|索引失效的情况]]
- 索引优化
    - 联合索引, 索引下推, 索引覆盖, index skip scan, 函数索引
- 长文本使用前缀索引
- like 左模糊优化成右模糊
- 只查自己需要的字段, 避免 `select *`
    - 如果有大对象如 TEXT, BLOB, 一个页存不下会发生**行溢出**[^1]
- `join` 使用小表驱动大表
- 深分页优化
- 连表查询需要注意不同字段的字符集是否一致, 否则可能会导致全表扫描[^2]
- Partitioning 分区
- 批量 batch 模式

---

- RC 模式减少锁冲突 (为什么大厂要把默认隔离级别改成 RC)
- OPTIMIZE TABLE
- 索引重建
- 分库分表
- 历史数据归档
- 读写分离
- 主从复制, 半同步复制
- 应用缓存

---

- MySQL 自带 `slow_query_log` 日志监控慢 sql
- 阿里 Druid 连接池的监控
- 应用接口耗时监控...

[^1]: [了解 MySQL的数据行、行溢出机制吗？](https://www.cnblogs.com/ZhuChangwu/p/14035330.html)
[^2]: [两张表编码方式不一致，关联查询一定会导致索引失效吗？](https://www.51cto.com/article/747278.html)