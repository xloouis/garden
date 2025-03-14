---
draft: false
tags:
  - java
  - concurrent
---
## 特性
- 互斥性
- 阻塞性
- 可重入性
- 非公平锁 (不遵循先来先服务的原则)
- 不可中断锁
## 实现
两种都依赖 **对象监视器 `monitor`**
- 基于 C++ ObjectMonitor
- 每个 Java 对象都内置一个 ObjectMonitor 对象
### 同步代码块
```java
public class SynchronizedDemo {
    public void method() {
        synchronized (this) {
            System.out.println("synchronized 代码块");
        }
    }
}
```
- **`monitorenter`** 指令指向同步代码块的开始位置
- **`monitorexit`** 指令指向同步代码块的结束位置
### 同步方法块
```java
public class SynchronizedDemo2 {
    public synchronized void method() {
        System.out.println("锁实例对象");
    }
	public static synchronized void method2() {
        System.out.println("锁类对象");
	}
}
```
- `ACC_SYNCHRONIZED` method 上的一个 flag (类元数据->方法区->运行时常量池)
## 偏向锁
JDK15默认关闭, JDK18废除
- 性能收益不明显
- JVM 内部代码维护成本高
## synchronized 可重入怎么实现的
#interview 
![[Pasted image 20250313213829.png]]
对象的 Markword 头信息中记录了线程ID
## 锁升级
[浅析synchronized锁升级的原理与实现](https://www.cnblogs.com/star95/p/17542850.html)

[[lock-details.png|细化流程图]]

![[Pasted image 20250313214735.png]]
## 锁优化
- 自旋锁
- 适应性自旋锁
- 锁消除
- 锁粗化
## See Also
- [Java锁与线程的那些事](https://tech.youzan.com/javasuo-yu-xian-cheng-de-na-xie-shi/)
- [synchronized 关键字](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#synchronized-%E5%85%B3%E9%94%AE%E5%AD%97)
- [[synchronized vs ReentrantLock]]