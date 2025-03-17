---
title: ⭐️ JVM 如何创建对象的
tags:
  - java
  - jvm
---
![[object-creation.png]]

1. 检查这个指令的参数是否能在常量池中定位到这个类的符号引用, 检查这个符号引用代表的类[[Java Object Lifecycle#生命周期|类加载]]是否完成(初始化完成), 如果没有先进行类加载
2. 分配内存
	- 如果开了 JIT, 进行逃逸分析, 如果无逃逸, 可以进行标量替换, ==对象栈上分配==
		- 存不下, 回退默认分配策略
	- 如果开了 TLAB, 相当于存入了预分配的 Eden 区
		- 存不下, 回退默认分配策略
	- 默认分配策略: 存入 Eden
		- 如果是[[Java Garbage Collection#新生代对象进入老年代的情况|大对象直接进堆]] (`-XX:PretenureSizeThreshold`)
		- 使用[[JVM Memory Structure#内存分配的两种方式|指针碰撞or空闲列表]]获取内存地址
		- 使用[[CAS]]+失败重试处理并发分配问题
3. 初始化零值 (比如 int=0, 对象引用=null)
4. 设置[[JVM Memory Structure#对象的内存布局|对象头]], 该实例所对应的类、如何才能找到类的元数据信息、对象的哈希码、对象的 GC 分代年龄，轻量级锁等等信息. 另外，根据虚拟机当前运行状态的不同，如是否启用偏向锁等，对象头会有不同的设置方式
5. 调用该类的构造方法 `<init>`, 初始化对象
6. 当对象完成创建之后, 返回一个该对象的引用

## See Also
- [[volatile#User a = new User(); 是原子性操作吗？|User a = new User(); 有几个步骤]]
- [对象的创建](https://javaguide.cn/java/jvm/memory-area.html#%E5%AF%B9%E8%B1%A1%E7%9A%84%E5%88%9B%E5%BB%BA)
- [JVM中对象创建与内存分配机制](https://blog.csdn.net/qq_40436854/article/details/109571649)