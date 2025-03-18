---
title: 临键锁
tags:
  - mysql
  - lock
---
![[next-key-lock.png]]

临键锁 - Next-Key Lock

- [[Shared and Exclusive Locks]] 有共享锁&排它锁
- 索引记录上的[[Record Locks|记录锁]], 和==索引记录之前间隙==上的[[Gap Locks|间隙锁]]的组合
- 只在 `Repeatable Reads` 下生效
- 锁的范围左开右闭

假设一个索引包含值10、11、13和20。此索引可能的next-key锁包括以下区间:
```text
(-∞, 10]
(10, 11]
(11, 13]
(13, 20]
(20, ∞ ]
```

对于最后一个间隙，∞不是一个真正的索引记录，因此，实际上，这个next-key锁只锁定最大索引值之后的间隙。  
 