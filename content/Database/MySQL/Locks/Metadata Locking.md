---
title: 字典锁
tags:
  - mysql
  - lock
---
字典锁，英文名叫做MetaData Lock，也叫做MDL锁，它是一种用于管理元数据的锁机制，而不是数据本身的锁。

MDL锁用于控制对数据库对象的元数据的并发访问，数据库会在执行DDL（Data Defination Language）操作时加上字典锁。字典锁的主要目的是保护数据库中的元数据对象，如表、列、索引、视图等，以确保在DDL操作期间，不会出现数据一致性问题和竞争条件。  
  
以下是触发数据库加字典锁的一些情况：  
1. 创建/修改/删除表结构：当执行CREATE TABLE、ALTER TABLE、DROP TABLE等DDL语句时，数据库会对相关的表和表的元数据对象加上字典锁，以阻止其他事务同时修改这些表的结构。  
2. 创建/修改/删除索引：执行CREATE INDEX、ALTER TABLE 添加索引、修改、删除索引等DDL操作时，会锁定与索引相关的元数据，以确保索引的一致性。  
3. 修改列定义：如果执行ALTER TABLE来修改表的列定义，例如改变数据类型、添加、删除、重命名列等，相关的列和表的元数据会被锁定。  
4. 创建/修改/删除视图：当执行CREATE VIEW、ALTER VIEW、DROP VIEW等DDL操作以创建或修改视图时，相关视图的元数据会被锁定。  
5. 其他DDL操作：其他的DDL操作，如创建、修改、删除存储过程、触发器、事件等也可能涉及到元数据的锁定。
## See Also

- [Metadata Locking](https://dev.mysql.com/doc/refman/8.4/en/metadata-locking.html)