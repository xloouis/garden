---
draft: false
tags:
  - hashmap
  - java
title: HashMap 初始容量设置
---

>[!quote] [阿里巴巴Java开发手册](https://alibaba.github.io/p3c/编程规约/集合处理.html)
>【推荐】集合初始化时，指定集合初始值大小。
> 
> 说明：HashMap使用HashMap(int initialCapacity) 初始化，
> 
> 正例：initialCapacity = (需要存储的元素个数 / 负载因子) + 1。注意负载因子（即loader factor）默认为0.75，如果暂时无法确定初始值大小，请设置为16（即默认值）。
> 
> 反例：HashMap需要放置1024个元素，由于没有设置容量初始大小，随着元素不断增加，容量7次被迫扩大，resize需要重建hash表，严重影响性能。

使用 `Guava`
```java
Map<String, String> map = Maps.newHashMapWithExpectedSize(7);
```

相关源码
```java
static int capacity(int expectedSize) {
    if (expectedSize < 3) {
        CollectPreconditions.checkNonnegative(expectedSize, "expectedSize");
        return expectedSize + 1;
    } else {
        return expectedSize < 1073741824 ? (int)((float)expectedSize / 0.75F + 1.0F) : 2147483647;
    }
}
```
