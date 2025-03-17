---
tags:
  - java
  - jvm
title: 类生命周期
---
![[object-loading.png]]

____

## 生命周期

- 类加载
	- 加载（Loading）[[ClassLoader]]
		1. 通过全类名获取定义此类的二进制字节流。
		2. 将字节流所代表的静态存储结构转换为方法区的运行时数据结构。
		3. 在内存中生成一个代表该类的 `Class` 对象，作为 [[JVM Memory Structure|方法区]] 这些数据的访问入口。
		- [[#数组是怎么被加载的]]
	- 连接/链接（Linking）
		- 验证（Verification）
			1. 文件格式验证（Class 文件格式检查）
			2. 元数据验证（字节码语义检查）
			3. 字节码验证（程序语义检查）
			4. 符号引用验证（类的正确性检查）
		- 准备（Preparation）
			- 正式为类变量分配内存并设置类变量初始值
		- 解析（Resolution）
			- 虚拟机将常量池内的符号引用替换为直接引用的过程
	- 初始化（Initialization）
		- 执行初始化方法 `<clinit> ()`方法的过程，是类加载的最后一步，这一步 JVM 才开始真正执行类中定义的 Java 程序代码(字节码)
		- [[#初始化的延迟加载|延迟加载的]]，除了一些基础的类以外，其他的类都是在需要使用类时才会进行加载
- 使用（Using）
- 卸载（Unloading）
	- 卸载类即该类的 Class 对象被 GC, 满足3个条件
		1. 该类的所有的实例对象都已被 GC，也就是说堆不存在该类的实例对象。
		2. 该类没有在其他任何地方被引用
		3. 该类的类加载器的实例已被 GC
	- 那些自定义类加载器一些场景的类会被回收掉，如tomcat，SPI，JSP等临时类，是存活不久的，所以需要来不断回收
## 初始化的延迟加载
对于初始化阶段，虚拟机严格规范了有且只有 6 种情况下，必须对类进行初始化(只有主动去使用类才会初始化类)：
1. 当遇到 `new`、 `getstatic`、`putstatic` 或 `invokestatic` 这 4 条字节码指令时，比如 `new` 一个类，读取一个静态字段(未被 final 修饰)、或调用一个类的静态方法时。
2. 使用 `java.lang.reflect` 包的方法对类进行反射调用时如 `Class.forName("...")`, `newInstance()` 等等。如果类没初始化，需要触发其初始化。
3. 初始化一个类，如果其父类还未初始化，则先触发该父类的初始化。
4. 当虚拟机启动时，用户需要定义一个要执行的主类 (包含 `main` 方法的那个类)，虚拟机会先初始化这个类。
5. `MethodHandle` 和 `VarHandle` 可以看作是轻量级的反射调用机制，而要想使用这 2 个调用, 就必须先使用 `findStaticVarHandle` 来初始化要调用的类。
6. **「补充，来自[issue745](https://github.com/Snailclimb/JavaGuide/issues/745 "issue745")」** 当一个接口中定义了 JDK8 新加入的默认方法（被 default 关键字修饰的接口方法）时，如果有这个接口的实现类发生了初始化，那该接口要在其之前被初始化。
## 数组是怎么被加载的
- 每个 Java 类都有一个引用指向加载它的 `ClassLoader`。不过，数组类不是通过 `ClassLoader` 创建的，而是 JVM 在需要的时候自动创建的
- 数组类通过`getClassLoader()`方法获取 `ClassLoader` 的时候和该数组的元素类型的 `ClassLoader` 是一致的
- 如果元素类型是原始类型，则数组类没有类加载器
## See Also
- [类加载过程详解](https://javaguide.cn/java/jvm/class-loading-process.html#%E5%8A%A0%E8%BD%BD)
