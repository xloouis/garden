---
tags:
  - java
  - concurrent
---
- [[synchronized]] 和 [[ReentrantLock]] 都是一次只允许一个线程访问某个资源，而`Semaphore`(信号量)可以用来控制同时访问特定资源的线程数量。
- 支持公平/非公平 `public Semaphore(int permits, boolean fair)`
	- `permits` 对应 [[AbstractQueuedSynchronizer]] 的 `state`
```java
// 初始共享资源数量
final Semaphore semaphore = new Semaphore(5);
// 获取1个许可
semaphore.acquire();
// 释放1个许可
semaphore.release();
```
## See Also
- [Semaphore详解](https://pdai.tech/md/java/thread/java-thread-x-juc-tool-semaphore.html#%E6%9B%B4%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3)