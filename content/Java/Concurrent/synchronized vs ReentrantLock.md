---
tags:
  - java
  - concurrent
  - interview
title: synchronized 和 ReentrantLock 区别
---
- 都是可重入锁
- [[synchronized]] 内置, [[ReentrantLock]] 通过代码实现
- synchronized 自动获取/释放锁
- ReentrantLock 具有响应中断, 超时等待等特性
- synchronized 只是非公平锁, ReentrantLock 公平非公平都能实现
## 虚拟线程
#todo 
synchronized 不建议和虚拟线程一起使用
## See Also
- [synchronized 和 ReentrantLock 有什么区别？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#%E2%AD%90%EF%B8%8Fsynchronized-%E5%92%8C-reentrantlock-%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)