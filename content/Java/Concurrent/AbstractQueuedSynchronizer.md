---
tags:
  - java
  - concurrent
title: ⭐️ AQS
---
AbstractQueuedSynchronizer (AQS, 抽象队列同步器)
## 使用模板方法模式
- `boolean isHeldExclusively()`
	- 该线程是否正在独占资源, 只有用到 Condition 才需要去实现它
- exclusive mode 独占模式
	- 一次只有一个线程可以获取同步状态
	- `state` 通常 0 表示未锁定
	- `boolean tryAcquire(int arg)`
	- `boolean tryRelease(int arg)`
- shared mode 共享模式
	- 允许多个线程同时获取同步状态
	- `state` 通常表示可用资源数量
	- `int tryAcquireShared(int arg)`
		- 负数表示失败; 0表示成功, 但没有剩余可用资源; 正数表示成功, 且有剩余资源
	- `boolean tryReleaseShared(int arg)`
## 数据结构
![[aqs.png]]
### state
```java
private volatile int state; // 同步状态
protected final boolean compareAndSetState(int expect, int update) {
	// See below for intrinsics setup to support this
	return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
}
```
[[CAS]] 和 [[volatile]] 在AQS中是互补的：CAS 提供原子性操作以避免锁的使用，而 volatile 确保修改的可见性和内存操作的有序性。两者结合，使得 AQS 能够以一种高效且线程安全的方式管理同步状态。
### FIFO 队列 - 同步队列
```java
static final class Node {
    // 前驱和后继节点，构成双向链表
    Node prev;
    Node next;
    // 线程本身
    Thread thread;
    // 状态信息，表示节点在同步队列中的等待状态
    int waitStatus;
    // ...
}
```
[CLH 变体队列](https://mp.weixin.qq.com/s/jEx-4XhNGOFdCo4Nou5tqg)
- 自旋 -> 自旋+阻塞
- 单项队列 -> 双向队列
### ConditionObject - 条件队列
```java
public class ConditionObject implements Condition, java.io.Serializable {
    // 条件队列的首尾节点
    private transient Node firstWaiter;
    private transient Node lastWaiter;
    // ...
}
```

`Condition#await()`, 释放锁, 加入条件队列, 直到被另一个线程的signal()或signalAll()方法唤醒, 或被中断

[ReentrantLock Condition使用详解](https://www.cnblogs.com/hongdada/p/6150699.html)
## 公平/非公平实现
- 非公平: 直接 CAS 尝试获取, 失败了创建 Node 放入队尾
- 公平: 找队列前序节点, 失败放入队尾
## 等待/唤醒实现
[park&unpark](https://pdai.tech/md/java/thread/java-thread-x-lock-LockSupport.html)
## 相关实现
- [[ReentrantLock]]
	- [[CyclicBarrier]], 使用了 ReentrantLock 实现
- ReentrantReadWriteLock
- [[CountDownLatch]]
- [[Semaphore]]
- ThreadPoolExecutor.Worker
## See Also
- [AQS详解](https://javaguide.cn/java/concurrent/aqs.html)
- [从ReentrantLock的实现看AQS的原理及应用](https://tech.meituan.com/2019/12/05/aqs-theory-and-apply.html)
- [锁核心类AQS详解](https://pdai.tech/md/java/thread/java-thread-x-lock-AbstractQueuedSynchronizer.html)
- [AQS 都看完了，Condition 原理可不能少！](https://cloud.tencent.com/developer/article/1750585)