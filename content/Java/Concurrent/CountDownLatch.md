---
tags:
  - java
  - concurrent
---
允许 `count` 个线程阻塞在一个地方，直至所有线程的任务都执行完毕。

1. `new CountDownLatch(int count)`
	- `count` -> [[AbstractQueuedSynchronizer]] 的 `state`
2. `countDown()` -> `state`-1
	- If the current count equals zero then nothing happens.
	- 减少到 0 不会再减
3. `await()` 阻塞

```java
CountDownLatch latch = new CountDownLatch(5); 
latch.countDown();
latch.countDown();
// ..........
latch.await();
```
