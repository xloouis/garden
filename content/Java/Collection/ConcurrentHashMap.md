---
draft: true
tags:
  - java
  - hashmap
  - concurrent
---
#todo 
## 线程安全
- 1.7 使用分段锁
	- 可以减少锁竞争，但是在高并发场景下，仍然会出现锁竞争，从而导致性能下降
- 1.8 节点锁, "CAS+synchronized"
	- 为空使用 [[CAS]] 添加新节点
	- 不为空 [[synchronized]] 锁 Node
	- 锁粒度更细
## 为什么用 [[synchronized]] 不用 [[ReentrantLock]]
同一个hashMap中，同时去写同一个节点的概率还是很低的。所以，这种情况下，并发冲突并不高, synchronized就不会频繁的升级为重量级锁

synchronized 优势:
- 无需手动锁管理，编程模型更简单
- JVM内置的语义，JVM能够在运行时作出相应的优化措施，比如锁粗化、锁消除等
- 当获取锁获取失败时，synchronized会通过自旋避免线程被挂起，而ReentrantLock 会导致线程挂起。而线程不需要挂起的话就可以减少线程上下文切换的开销
- 内存开销小. ReentrantLock 是一个独立的对象，而 synchronized 是利用对象头（Object Header）中的一部分位标记来实现的锁
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
