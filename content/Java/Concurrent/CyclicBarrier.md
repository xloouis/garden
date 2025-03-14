---
tags:
  - java
  - concurrent
---
- CyclicBarrier是一个同步屏障，可循环使用（Cyclic）的屏障（Barrier）. 它允许多个线程相互等待，直到到达某个公共屏障点 (parties)，才能继续执行。它通常用来实现多个线程在同一个屏障处等待，然后再一起继续执行的操作
- 使用 [[ReentrantLock]] 实现

```java
class MyThread extends Thread {
    private CyclicBarrier cb;
    public MyThread(String name, CyclicBarrier cb) {
        super(name);
        this.cb = cb;
    }
    
    public void run() {
        System.out.println(Thread.currentThread().getName() + " going to await");
        try {
            cb.await();
            System.out.println(Thread.currentThread().getName() + " continue");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
public class CyclicBarrierDemo {
    public static void main(String[] args) throws InterruptedException, BrokenBarrierException {
        CyclicBarrier cb = new CyclicBarrier(3, new Thread("barrierAction") {
            public void run() {
                System.out.println(Thread.currentThread().getName() + " barrier action");
            }
        });
        MyThread t1 = new MyThread("t1", cb);
        MyThread t2 = new MyThread("t2", cb);
        t1.start();
        t2.start();
        System.out.println(Thread.currentThread().getName() + " going to await");
        cb.await();
        System.out.println(Thread.currentThread().getName() + " continue");
    }
}
```
## See Also
- [CyclicBarrier详解](https://pdai.tech/md/java/thread/java-thread-x-juc-tool-cyclicbarrier.html)
