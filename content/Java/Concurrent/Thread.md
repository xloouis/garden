---
draft: false
tags:
  - java
  - concurrent
---
## 生命周期
[说说线程的生命周期和状态?](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#%E2%AD%90%EF%B8%8F%E8%AF%B4%E8%AF%B4%E7%BA%BF%E7%A8%8B%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%92%8C%E7%8A%B6%E6%80%81)
![[thread.png]]
## 创建方式
- extends Thread
- implements Runnable
- implements Callable

	```java
	Callable<String> callable = () -> {
	    return "hello world";
	};
	
	FutureTask<String> futureTask = new FutureTask<>(callable);
	Thread thread = new Thread(futureTask);
	thread.start();
	```
- 线程池
## run vs start
- start 启动线程
- run 直接执行
## Thread#sleep vs Object#wait
https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#thread-sleep-方法和-object-wait-方法对比
## Thread.join()
```java
public static void main(String[] args) throws InterruptedException {
    Thread thread1 = new Thread(new Runnable() {
        @Override
        public void run() {
            try {
                Thread.sleep(10000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("Thread 1 running");
        }
    });

    thread1.start();
	// main 主线程等待 thread1 执行完成
	thread1.join();

    System.out.println("Main 1 running");
}
```
## Thread.sleep(0)的作用
让线程主动释放CPU时间片, 让其他线程可以进行一次公平的争抢
## Thread.isAlive()
- 判断线程是否存活
- 如果被锁, 比如 `synchronized (t)`, `t.isAlive()` 返回 `true`
## 线程同步的方式
- [[synchronized]]
- [[volatile]] 
- [[ReentrantLock]]
- [[Semaphore]]
- [[CountDownLatch]]
- [[CyclicBarrier]]
- [Phaser](https://blog.csdn.net/liuyu973971883/article/details/107917079)
## See Also
- [大家都说Java有三种创建线程的方式！并发编程中的惊天骗局！](https://mp.weixin.qq.com/s/NspUsyhEmKnJ-4OprRFp9g)
- [不可不说的Java“锁”事](https://tech.meituan.com/2018/11/15/java-lock.html)