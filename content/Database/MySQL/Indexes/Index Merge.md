---
tags:
    - mysql
---

> [!warning] 一条 SQL 是有可能用多个索引的
 
> [!quote] 索引合并 - index merge
> The Index Merge access method retrieves rows with multiple [`range`](https://dev.mysql.com/doc/refman/8.4/en/explain-output.html#jointype_range) scans and merges their results into one. This access method merges index scans from a single table only, not scans across multiple tables. The merge can produce unions, intersections, or unions-of-intersections of its underlying scans.
> 
> MySQL 5.1 引入, 适用于==一张表==内, 合并多个 `range` 索引扫描的结果, 来优化查询

---

## EXPLAIN 输出

- `type`: 显示 `index_merge`
- `key`: 包含使用到的索引
- `key_len`: 以列表形式展示每个合并索引中最长键部分的长度
- `Extra`: 三种算法
    - `Using intersect(...)`
    - `Using union(...)`
    - `Using sort_union(...)`


## See Also

- [Index Merge Optimization](https://dev.mysql.com/doc/refman/8.4/en/index-merge-optimization.html)
