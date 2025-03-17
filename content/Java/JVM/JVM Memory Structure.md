---
tags:
  - java
  - jvm
draft: false
title: JVM 内存区域
---
> [!info]- 内存区域图
> ![[jvm1.7.png]]
> ![[jvm1.8.png]]
> ![[method area.png]]

## 可能出现的异常
- 程序计数器
	- 唯一一个不会出现 OutOfMemoryError 的内存区域
- 栈/本地方法栈
	- StackOverFlowError
	- OutOfMemoryError
		- 对象栈上分配 (JIT优化, 逃逸分析, 标量替换)
- 堆
	- OutOfMemoryError: GC Overhead Limit Exceeded
		- 当 JVM 花太多时间执行垃圾回收并且只能回收很少的堆空间时，就会发生此错误。
	- OutOfMemoryError: Java heap space
		- 堆内存空间不够
- 元空间
	- OutOfMemoryError: MetaSpace
## 内存分配的两种方式
- 指针碰撞
	- 适用场合：堆内存规整（即没有内存碎片）的情况下。
	- 原理：用过的内存全部整合到一边，没有用过的内存放在另一边，中间有一个分界指针，只需要向着没用过的内存方向将该指针移动对象内存大小位置即可。
	- Serial
	- ParNew
- 空闲列表
	- 适用场合：堆内存不规整的情况下。
	- 原理：虚拟机会维护一个列表，该列表中会记录哪些内存块是可用的，在分配的时候，找一块儿足够大的内存块儿来划分给对象实例，最后更新列表记录。
	- CMS
## 内存分配并发问题
- [[CAS]]+失败重试
- TLAB (Thread-Local Allocation Buffer)
## 对象的内存布局
- [[Object Header|对象头]] (Header)
	- 标记字段（Mark Word）
		- 哈希码（HashCode）、GC 分代年龄、锁状态标志、线程持有的锁、偏向线程 ID、偏向时间戳等等
	- 类型指针（Klass pointer）
		- 对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例
	- 数组长度 (Array Length)
		- 如果对象是一个数组，那么对象头还需要有额外的空间用于存储数组的长度
- 实例数据（Instance Data）
- 对齐填充（Padding）
	- **仅仅起占位作用。** 因为 Hotspot 虚拟机的自动内存管理系统要求对象起始地址必须是 8 字节的整数倍
## See Also
- [Java内存区域详解](https://javaguide.cn/java/jvm/memory-area.html#%E8%BF%90%E8%A1%8C%E6%97%B6%E6%95%B0%E6%8D%AE%E5%8C%BA%E5%9F%9F)