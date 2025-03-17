---
tags:
  - java
  - jvm
---

## 使用场景

- [jdbc加载多个不同版本驱动jar包](https://blog.csdn.net/huanshen7428/article/details/126390600)

- [在一个项目中, 同时引用不同版本同一个jar包](https://juejin.cn/post/7255146316181880891)

- [Java同时引用不同版本相同的jar包](https://blog.tntao.cn/archives/1690275821104)

## 在[[Java Object Lifecycle#生命周期|加载（Loading）]]阶段

> [!quote]
> 类加载器的主要作用就是加载 Java 类的字节码（ `.class` 文件）到 JVM 中（在内存中生成一个代表该类的 `Class` 对象）。
> - 类加载器是一个负责加载类的对象，用于实现类加载过程中的加载这一步。
> - 每个 Java 类都有一个引用指向加载它的 `ClassLoader`。
> - 数组类不是通过 `ClassLoader` 创建的（数组类没有对应的二进制字节流），是由 JVM 直接生成的。

![[classloader.png]]
- **`BootstrapClassLoader`(启动类加载器)**
	- 最顶层的加载类，由 C++实现，通常表示为 null，并且没有父级，主要用来加载 JDK 内部的核心类库（ `%JAVA_HOME%/lib`目录下的 `rt.jar`、`resources.jar`、`charsets.jar`等 jar 包和类）以及被 `-Xbootclasspath`参数指定的路径下的所有类。
- **`ExtensionClassLoader`(扩展类加载器)**
	- 主要负责加载 `%JRE_HOME%/lib/ext` 目录下的 jar 包和类以及被 `java.ext.dirs` 系统变量所指定的路径下的所有类。
	- JDK1.9 重命名为平台类加载器 `PlatformClassLoader`
- **`AppClassLoader`(应用程序类加载器)**
	- 面向我们用户的加载器，负责加载当前应用 classpath 下的所有 jar 包和类。

## 双亲委派模型 (Parents Delegation Model)

双亲委派模型主要是由ClassLoader#loadClass实现的.

> [!info]- `loadClass` 执行流程
> ```java
> protected Class<?> loadClass(String name, boolean resolve)
>     throws ClassNotFoundException
> {
>     synchronized (getClassLoadingLock(name)) {
>         //首先，检查该类是否已经加载过
>         Class c = findLoadedClass(name);
>         if (c == null) {
>             //如果 c 为 null，则说明该类没有被加载过
>             long t0 = System.nanoTime();
>             try {
>                 if (parent != null) {
>                     //当父类的加载器不为空，则通过父类的loadClass来加载该类
>                     c = parent.loadClass(name, false);
>                 } else {
>                     //当父类的加载器为空，则调用启动类加载器来加载该类
>                     c = findBootstrapClassOrNull(name);
>                 }
>             } catch (ClassNotFoundException e) {
>                 //非空父类的类加载器无法找到相应的类，则抛出异常
>             }
> 
>             if (c == null) {
>                 //当父类加载器无法加载时，则调用findClass方法来加载该类
>                 //用户可通过覆写该方法，来自定义类加载器
>                 long t1 = System.nanoTime();
>                 c = findClass(name);
> 
>                 //用于统计类加载器相关的信息
>                 sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
>                 sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
>                 sun.misc.PerfCounter.getFindClasses().increment();
>             }
>         }
>         if (resolve) {
>             //对类进行link操作
>             resolveClass(c);
>         }
>         return c;
>     }
> }
> ```

- `protected Class loadClass(String name, boolean resolve)`
	- 如果想打破双亲委派, 重写 loadClass
	- 加载指定二进制名称的类，实现了双亲委派机制 。`name` 为类的二进制名称，`resolve` 如果为 true，在加载时调用`resolveClass(Class<?> c)` 方法解析该类。
- `protected Class findClass(String name)`
	- ==如果不想打破, 重写 findClass==
		- 如果父类加载器加载失败, 则调用自己的 findClass
	- 根据类的二进制名称来查找类，默认实现是空方法。



## SPI 打破双亲委派

基础类要加载用户自定义的类, 父加载子

```java title="ServiceLoader"
public static <S> ServiceLoader<S> load(Class<S> service) {
		ClassLoader cl = Thread.currentThread().getContextClassLoader();
		return ServiceLoader.load(service, cl);
}
```

线程上下文类加载器(Thread Context ClassLoader)

- 线程上下文类加载器的原理是将一个类加载器保存在线程私有数据里，跟线程绑定，然后在需要的时候取出来使用

- 这个类加载器通常是由应用程序或者容器（如 Tomcat）设置的。

- Java.lang.Thread 中的getContextClassLoader()和 setContextClassLoader(ClassLoader cl)分别用来获取和设置线程的上下文类加载器。如果没有通过setContextClassLoader(ClassLoader cl)进行设置的话，线程将继承其父线程的上下文类加载器。


## Tomcat 打破双亲委派

### 为什么要破坏

- 一个Tomcat，是可以同时运行多个应用的，而不同的应用可能会同时依赖一些相同的类库，但是他们使用的版本可能是不一样的，但是这些类库中的Class的全路径名因为是一样的，如果都采用双亲委派的机制的话，是无法重复加载同一个类的，那么就会导致版本冲突。

- 而为了有更好的隔离性，所以在Tomcat中，每个应用都由一个独立的WebappClassLoader进行加载，这样就可以完全隔离开。而多个WebAppClassLoader之间是没有委派关系的，他们就是各自加载各自需要加载的Jar包。

- 由于每个Web应用程序都有自己的类加载器，因此不同Web应用程序中的类可以使用相同的类名，而不会产生命名冲突。

### 类加载器

```text
-- 默认
  Bootstrap
     |
   System
     |
   Common
   /     \
Webapp1   Webapp2 ...

-- 需要配置 server.loader and/or shared.loader
  Bootstrap
      |
    System
      |
    Common
     /  \
Server  Shared
         /  \
   Webapp1  Webapp2 ...
```

### 加载顺序

配置项 `delegate`
- 默认 `false`, Bootstrap->WebApp->System->Common
  - 先Bootstrap尝试加载, 避免JRE中的核心类被我们应用自己给覆盖(如String等)
- `true`, 严格遵循双亲委派, Bootstrap->System->Common->WebApp

## 判断两个类是否相同

- ClassLoader 类加载器一样
- 类的全名是否相同
## 破坏双亲委派, 能重写String类吗
不能

使用 `ClassLoader#defineClass` 方法将字节流转换为一个JVM识别的class时

会调用 `preDefineClass`, 里面会校验 `name.startsWith("java.")`, 也就是不能以 `java.` 开头

## Class.forName 和 ClassLoader 区别

[学了这么久的java反射机制，你知道class.forName和classloader的区别吗？](https://developer.aliyun.com/article/952677)

- class.forName()除了将类的.class文件加载到jvm中之外，还会对类进行解释，执行类中的static块。当然还可以指定是否执行静态块。
- classLoader只干一件事情，就是将.class文件加载到jvm中，不会执行static中的内容,只有在newInstance才会去执行static块。

## Spring Boot 中为什么要打破双亲委派

#todo 

[[LaunchedURLClassLoader]]

## JDK9 ClassLoader 新特性

#todo 

[Java9及之后关于类加载器的新特性](https://blog.csdn.net/cristianoxm/article/details/134898274)
## See Also

- [Tomcat文档](https://tomcat.apache.org/tomcat-9.0-doc/class-loader-howto.html)
- [类加载器详解](https://javaguide.cn/java/jvm/classloader.html)
- [深度思考：老生常谈的双亲委派机制，JDBC、Tomcat是怎么反其道而行之的？](https://blog.csdn.net/qq_33591903/article/details/120088757)
- [ClassLoader（类加载机制）](https://www.javasec.org/javase/ClassLoader/)