---
tags:
  - java
  - concurrent
draft: true
title:
---
- ThreadPoolExecutor
- ScheduledThreadPoolExecutor
## 最佳实践
使用有界队列, 控制线程创建数量

可以使用 guava 来创建
```java
ThreadFactory threadFactory = new ThreadFactoryBuilder()
                        .setNameFormat(threadNamePrefix + "-%d")
                        .build();
ExecutorService threadPool = new ThreadPoolExecutor(corePoolSize, maximumPoolSize, keepAliveTime, TimeUnit.MINUTES, workQueue, threadFactory)
```
## 线程池预热
- `prestartCoreThread()` 启动一个核心线程
- `prestartAllCoreThreads()` 启动所有核心线程
## 有异常, 线程销毁还是复用
[“线程池中线程异常后：销毁还是复用？”](https://mp.weixin.qq.com/s/9ODjdUU-EwQFF5PrnzOGfw)
- 使用 `execute()` 时，未捕获异常导致线程终止，线程池创建新线程替代
- 使用 `submit()` 时，异常被封装在 `Future` 中，线程继续复用。
## Libraries
- [Hippo4j](https://github.com/opengoofy/hippo4j)
- [DynamicTP](https://github.com/dromara/dynamic-tp)
## See Also
- [Java 线程池详解](https://javaguide.cn/java/concurrent/java-thread-pool-summary.html)