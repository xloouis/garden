---
title: 对象头
tags:
  - java
  - jvm
---
![[object-header2.png]]
## Mark Word (标记字段)
![[object-header-image.png]]
对象头中主要包含了GC分代年龄、锁状态标记、哈希码、epoch等信息。
  
从上图中可以看出，对象的状态一共有五种，分别是无锁态、轻量级锁、重量级锁、GC标记和偏向锁。在32位的虚拟机中有两个Bits是用来存储锁的标记为的，但是我们都知道，两个bits最多只能表示四种状态：00、01、10、11，那么第五种状态如何表示呢 ，就要额外依赖1Bit的空间，使用0和1来区分。

## Klass Pointer (Class 对象指针)

对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例。

## Array Length (数组长度)

如果对象是一个数组，那么对象头还需要有额外的空间用于存储数组的长度。
## See Also
- [JAVA 对象头分析](https://www.cnblogs.com/hongdada/p/14087177.html)