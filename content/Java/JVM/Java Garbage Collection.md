---
draft: false
tags:
  - java
  - jvm
  - gc
title: JVM 垃圾回收
---
>[!quote]
>针对 HotSpot VM 的实现，它里面的 GC 其实准确分类只有两大种：
>
>部分收集 (Partial GC)：
>
>- 新生代收集（Minor GC / Young GC）：只对新生代进行垃圾收集；
>- 老年代收集（Major GC / Old GC）：只对老年代进行垃圾收集。需要注意的是 Major GC 在有的语境中也用于指代整堆收集；
>- 混合收集（Mixed GC）：对整个新生代和部分老年代进行垃圾收集。
>
>整堆收集 (Full GC)：收集整个 Java 堆和方法区。
>
> ----
>
>著作权归JavaGuide(javaguide.cn)所有 基于MIT协议 原文链接：https://javaguide.cn/java/jvm/jvm-garbage-collection.html

![[jgc.png]]
## 为什么要STW
> Java中Stop-The-World机制简称STW，是在执行垃圾收集算法时，Java应用程序的其他所有线程都被挂起。这是Java中一种全局暂停现象，全局停顿，所有Java代码停止，native代码可以执行，但不能与JVM交互。

- 如果不暂停用户线程，就意味着期间会不断有垃圾产生，永远也清理不干净。
- 用户线程的运行必然会导致对象的引用关系发生改变，这就会导致两种情况：漏标和多标。

- 漏标：一个对象本来应该是存活对象，但是没有被正确的标记上，导致被错误的垃圾回收掉了。
- 多标：其实就是这个对象原本应该被回收掉的垃圾对象，但是被错误的标记成了存活对象。从而导致这个对象没有被GC回收掉。 这种情况还好一点，无非就是产生了一些浮动垃圾，下次GC再清理就好了。
## 为什么分代
分代垃圾收集可以将关注点集中在最近被分配的对象上，而无需整堆扫描，避免长命对象的拷贝，同时独立收集有助于降低响应时间。

Java中的大部分对象都是朝生夕死的, 通过将不同时期的对象存储在不同的内存池中，就可以节省宝贵的时间和空间，从而改善系统的性能。
## 新生代对象进入老年代的情况
- 达到默认 Age 年龄, 就进入老年代
- 大对象直接进入老年代, 参数: `-XX:PretenureSizeThreshold`
- 动态对象年龄判定
	- 虚拟机并不是永远地要求对象的年龄必须达到 MaxTenuringThreshold 才能晋升老年代，如果在 Survivor 中相同年龄所有对象大小的总和大于 Survivor 空间的一半，则年龄大于或等于该年龄的对象可以直接进入老年代，无需等到 MaxTenuringThreshold 中要求的年龄。
	- Hotspot 遍历所有对象时，按照年龄从小到大对其所占用的大小进行累积，当累积的某个年龄大小超过了 survivor 区的 50% 时（默认值是 50%，可以通过 `-XX:TargetSurvivorRatio=percent` 来设置，参见 [issue1199](https://github.com/Snailclimb/JavaGuide/issues/1199) ），取这个年龄和 MaxTenuringThreshold 中更小的一个值，作为新的晋升年龄阈值
## GC 触发条件
- YoungGC
	- 年轻代中的Eden区分配满
- FullGC
	- 老年代空间不足  
		- 创建一个大对象，超过指定阈值会直接保存在老年代当中，如果老年代空间也不足，会触发Full GC。  
		- YoungGC之后，发现要移到老年代的对象，老年代存不下的时候，会触发一次FullGC
	-  [[Java Garbage Collection#空间分配担保|空间分配担保]]失败, 触发 FullGC
	- 永久代空间不足 (JDK1.8之后没有)
	- 手动执行 `System.gc()`
		- 并不保证一定会立即触发
## 空间分配担保
在发生 Minor GC 之前，虚拟机先检查老年代最大可用的连续空间是否大于新生代所有对象总空间，如果条件成立的话，那么 Minor GC 可以确认是安全的。

如果不成立的话虚拟机会查看 HandlePromotionFailure 设置值是否允许担保失败，如果允许那么就会继续检查老年代最大可用的连续空间是否大于历次晋升到老年代对象的平均大小，如果大于，将尝试着进行一次 Minor GC；如果小于，或者 HandlePromotionFailure 设置不允许冒险，那么就要进行一次 Full GC。

- 剩余的存活对象大小，小于Survivor区，那就直接进入Survivor区。  
- 剩余的存活对象大小，大于Survivor区，小于老年代可用内存，那就直接去老年代。  
- 剩余的存活对象大小，大于Survivor并且大于老年代，触发"FullGC"。

![[minorgc-ensure.png]]
## 死亡对象判断方法
- 引用计数法
- 可达性分析算法
	- GC Roots
		- 虚拟机栈(栈帧中的局部变量表)中引用的对象
		- 本地方法栈(Native 方法)中引用的对象
		- 方法区中类静态属性引用的对象
		- 方法区中常量引用的对象
		- 所有被同步锁持有的对象
		- JNI（Java Native Interface）引用的对象
	- 两次标记
		1. 第一次标记通过可达性分析算法。如果没有GC Roots相连接的引用链，那么将第一次标记  
		2. 如果对象的finalize()方法被覆盖并且没有执行过，则放在F-Queue队列中等待执行(不一定会执行)，如果一段时间后该队列的finalize()方法被执行且和GC Roots关联，则移出“即将回收”集合。如果仍然没有关联，则进行第二次标记，才会对该对象进行回收. 不过现在都不提倡覆盖finalize方法，它的本意是像Cpp一样在对象销毁前执行，但是它影响了JAVA的安全和GC的性能，所以第二种判断会越来越少
- [[tri-color marking | 三色标记算法]] (并发标记算法)
## 垃圾回收算法
- 标记-清除（Mark-and-Sweep）
	- **效率问题**：标记和清除两个过程效率都不高。
	- **空间问题**：标记清除后会产生大量不连续的内存碎片。
- 复制算法 (Copying)
	- **可用内存变小**：可用内存缩小为原来的一半。
	- **不适合老年代**：如果存活对象数量比较大，复制性能会变得很差。
- 标记-整理（Mark-and-Compact）
	- 由于多了整理这一步，因此效率也不高，适合老年代这种垃圾回收频率不是很高的场景。
- 分代收集算法
## 垃圾收集器
![[garbage collector.png]]
- Serial
	- 单线程
	- 新生代采用标记-复制算法，老年代采用标记-整理算法。
- Serial Old
	- 单线程
	- 标记-整理算法
	- 与 Parallel Scavenge 搭配使用
	- CMS 后备方案
- ParNew
	- Serial 的多线程版本
	- 新生代采用标记-复制算法，老年代采用标记-整理算法。
- Parallel Scavenge
	- 关注吞吐量
	- 新生代采用标记-复制算法，老年代采用标记-整理算法。
- Parallel Old
	- 使用多线程和“标记-整理”算法。在注重吞吐量以及 CPU 资源的场合，都可以优先考虑 Parallel Scavenge 收集器和 Parallel Old 收集器。
- CMS（Concurrent Mark Sweep）
	- 以获取最短回收停顿时间为目标的收集器
	- 实现了让垃圾收集线程与用户线程（基本上）同时工作
	- ==标记清除算法==
	- 三色标记法——增量更新解决漏标
	- 优点
		- 并发收集、低停顿
	- 缺点
		- 对 CPU 资源敏感
		- 无法处理浮动垃圾
		- 它使用的回收算法-“标记-清除”算法会导致收集结束时会有大量空间碎片产生
- G1 (Garbage-First)
	- 标记-复制算法回收年轻代  
	- 标记-整理算法回收老年代
	- 三色标记法——原始快照解决漏标
	- 主要针对配备多颗处理器及大容量内存的机器
	- 以极高概率满足 GC 停顿时间要求的同时,还具备高吞吐量性能特征
- ZGC
	- 标记-复制算法
	- 可以将暂停时间控制在几毫秒以内，且暂停时间不受堆内存大小的影响，出现 Stop The World 的情况会更少，但代价是牺牲了一些吞吐量
	- ZGC 最大支持 16TB 的堆内存
## See Also
- [JVM垃圾回收详解](https://javaguide.cn/java/jvm/jvm-garbage-collection.html)
- [Java垃圾回收基础知识](https://pdai.tech/md/java/jvm/java-jvm-gc.html)
- [面试官问我G1回收器怎么知道你是什么时候的垃圾?](https://www.cnblogs.com/thisiswhy/p/12388638.html)
- [新一代垃圾回收器ZGC的探索与实践](https://tech.meituan.com/2020/08/06/new-zgc-practice-in-meituan.html)