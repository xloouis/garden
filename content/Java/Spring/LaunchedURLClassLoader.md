---
tags:
  - spring
  - java
---
#todo 

Spring Boot 打破了传统的[[ClassLoader#双亲委派模型 (Parents Delegation Model)|双亲委派模型]]
- 使用 [LaunchedURLClassLoader](https://github.com/joansmith/spring-boot/blob/master/spring-boot-tools/spring-boot-loader/src/main/java/org/springframework/boot/loader/LaunchedURLClassLoader.java) 进行类加载
- 主要为了支持 "Fat JAR"（可执行 JAR） 的运行
	- 传统的 Application ClassLoader 只能从 外部 classpath 加载类，无法直接加载 JAR 包内嵌的其他 JAR（fat jar）。 因此 Spring Boot 需要自定义类加载器。


