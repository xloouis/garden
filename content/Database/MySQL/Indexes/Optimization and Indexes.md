---
draft: true
title: 索引和优化
tags:
---

## 8.0 以下版本的缓存

SQL_NO_CACHE

## Explain

统计的行数可能不准
优化器可能会选错
analyze table 重新统计索引信息
强制走索引

## 联合索引
- 最左匹配原则, 范围查询(>, <), 会停止匹配. (>=, <=, between, like前缀匹配的范围查询, 并不会停止匹配)
- 覆盖索引(索引覆盖)
- 索引下推 (index condition pushdown) `Using index condition`
- 建联合索引时, 把区分度大的字段排在前面
- 联合索引进行排序
- 经常用于 `group by` 和 `order by`, 这样查询的时候不需要再做一次排序了

index skip scan

## 索引优化

#todo orderby, groupby, where, join on 怎么用索引的, 能同时用吗

- 前缀索引优化
    - order by 无法使用前缀索引
    - 无法把前缀索引作覆盖索引
- 覆盖索引
- 主键索引最好自增
    - 不连续会导致页分裂
    - 主键长度越小, 二级索引叶子节点越小
- 索引列最好设置为NOT NULL [link](https://www.xiaolincoding.com/mysql/index/index_interview.html#%E6%8C%89%E5%AD%97%E6%AE%B5%E4%B8%AA%E6%95%B0%E5%88%86%E7%B1%BB)
- 防止[[Index Invalidation|索引失效]]

## change_buffer
写多读少
## 前缀索引
## flush操作

## count(1) 和 count(\*)

- 性能一样
- count(\*) 会被转换成 count(0)
- 如果有多个二级索引, 走 key_len 最小的二级索引
- 如果没有二级索引, 走聚簇索引
