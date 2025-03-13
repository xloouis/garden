---
draft: true
tags:
  - java
  - hashmap
---
ConcurrentHashMap在JDK 1.8之前使用分段锁保证线程安全， ConcurrentHashMap默认情况下将hash表分为16个桶（分片），在加锁的时候，针对每个单独的分片进行加锁，其他分片不受影响。锁的粒度更细，所以他的性能更好。  
ConcurrentHashMap在JDK 1.8中，采用了一种新的方式来实现线程安全，即使用了CAS+synchronized，这个实现被称为"分段锁"的变种，也被称为"锁分离"，它将锁定粒度更细，把锁的粒度从整个Map降低到了单个桶。

## 为什么不允许 null
>[!quote] Doug Lea - ConcurrentHashMap 作者
>The main reason that nulls aren't allowed in ConcurrentMaps (ConcurrentHashMaps, ConcurrentSkipListMaps) is that ambiguities that may be just barely tolerable in non-concurrent maps can't be accommodated. The main one is that if `map.get(key)` returns `null`, you can't detect whether the key explicitly maps to `null` vs the key isn't mapped. In a non-concurrent map, you can check this via `map.contains(key)`, but in a concurrent one, the map might have changed between calls.
>
>Further digressing: I personally think that allowing
nulls in Maps (also Sets) is an open invitation for programs
to contain errors that remain undetected until
they break at just the wrong time. (Whether to allow nulls even
in non-concurrent Maps/Sets is one of the few design issues surrounding
Collections that Josh Bloch and I have long disagreed about.)

避免二义性, `get(key)` 返回 `null` 有两种情况
1. value 为 null
2. map 里没有这个 key

- HashMap 可以用 `containsKey` 检查
- ConcurrentHashMap `containsKey` 不准确, 可能被其他线程修改
## See Also
- [ConcurrentHashMap详解](https://pdai.tech/md/java/thread/java-thread-x-juc-collection-ConcurrentHashMap.html)
