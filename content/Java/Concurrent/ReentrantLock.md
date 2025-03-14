---
tags:
  - java
  - concurrent
---
## 特性
- 可重入 #interview 
	- 加锁 [[AbstractQueuedSynchronizer]] `state` +1, 解锁 -1
- 公平/非公平, 默认非公平, `ReentrantLock(boolean fair)`
- 支持超时, `tryLock(long timeout, TimeUnit unit)`
- 等待可中断, `lockInterruptibly() throws InterruptedException`
	- `lock()` 会忽略异常继续等待获取锁

```java
public class Counter {
	private final Lock lock = new ReentrantLock();
	private int count;
	public void add(int n) {
		lock.lock();
		try {
			count += n;
		} finally {
			lock.unlock();
		}
	}
}
```
## See Also
- [[synchronized vs ReentrantLock]]
- [ReentrantLock Condition使用详解](https://www.cnblogs.com/hongdada/p/6150699.html)