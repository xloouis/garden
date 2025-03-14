---
title: CAS
tags:
  - java
  - concurrent
---
CAS - Compare And Swap
## 特性
- 用于实现乐观锁/自旋锁
- 基于 `Unsafe` 实现
- ==只保证了原子性==
	- 靠硬件实现, 执行 cmpxchg 指令时, 处理器会自动锁定总线
	- [CAS操作在ARM和x86下的不同实现](https://cloud.tencent.com/developer/article/2028582)

#todo
另外，CAS还有一个应用，那就是在JVM创建对象的过程中。  
  
对象创建在虚拟机中是非常频繁的。即使是仅仅修改一个指针所指向的位置，在并发情况下也不是线程安全的，可能正在给对象A分配内存空间，指针还没来得及修改，对象B又同时使用了原来的指针来分配内存的情况。  
  
解决这个问题的方案有两种，其中一种就是采用CAS配上失败重试的方式保证更新操作的原子性。
## 存在的问题
- ABA 问题
	- A 被改成 B, 然后 B 又被改成 A, 值没变, 但是被别人修改过了
	- `AtomicStampedReference`, 增加了 `stamp` 版本号, 可以解决
- 循环时间长开销大 (忙等待)
	- CAS 经常会用到自旋操作来进行重试
- 只能保证一个共享变量的原子操作
	- `AtomicReference` 类, 保证引用对象之间的原子性
## CAS 自旋
CAS 本身不自旋, 通常情况下, CAS 操作都会采用自旋的方式. 当 CAS 失败时，会重新尝试执行 CAS 操作, 直到操作成功或达到最大重试次数为止.
```java
// Unsafe#getAndAddInt 原子地获取并增加整数值
public final int getAndAddInt(Object o, long offset, int delta) {
    int v;
    do {
        // 以 volatile 方式获取对象 o 在内存偏移量 offset 处的整数值
        v = getIntVolatile(o, offset);
    } while (!compareAndSwapInt(o, offset, v, v + delta));
    // 返回旧值
    return v;
}
```
## CAS+自旋实现单例
实际工作中不要用这种方法
```java
public class Singleton {
    private static final AtomicReference<Singleton> INSTANCE =
		new AtomicReference<Singleton>(); 

    private Singleton() {}

    public static Singleton getInstance() {
        for (;;) {
            Singleton singleton = INSTANCE.get();
            if (null != singleton) {
                return singleton;
            }

            singleton = new Singleton();
            if (INSTANCE.compareAndSet(null, singleton)) {
                return singleton;
            }
        }
    }
}
```
## See Also
- [cas是如何实现的](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#java-%E4%B8%AD-cas-%E6%98%AF%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E7%9A%84)
- [CAS, Unsafe和原子类详解](https://pdai.tech/md/java/thread/java-thread-x-juc-AtomicInteger.html)
