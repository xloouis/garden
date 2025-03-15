---
tags:
  - java
  - jvm
draft: false
title: Java 内存区域
---
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
	- Serial
	- ParNew
- 空闲列表
	- CMS
## 内存分配并发问题
- [[CAS]]+失败重试
- TLAB (Thread-Local Allocation Buffer)
## See Also
- [Java内存区域详解](https://javaguide.cn/java/jvm/memory-area.html#%E8%BF%90%E8%A1%8C%E6%97%B6%E6%95%B0%E6%8D%AE%E5%8C%BA%E5%9F%9F)