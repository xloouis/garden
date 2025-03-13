---
draft: false
tags:
  - java
  - hashmap
---
## 数据结构
- 哈希表 + 拉链法(链地址法)
- 1.8 使用红黑树
## 参数
- DEFAULT_INITIAL_CAPACITY 初始容量
	- 默认 16
	- 必须是 2^n, 初始化计算的时候找到比传入的指定值大的第一个2的幂
	- 懒加载, 第一次 `put` 才初始化
	- [[HashMap initial capacity|初始容量设置多少合适]]
- DEFAULT_LOAD_FACTOR 负载系数
	- 默认 0.75f
		- 在时间和空间成本之间提供了很好的权衡
		- 0.75 * capacity 的值永远是整数
- threshold 扩容阈值
	- ++size > (capacity * load) 时
		- resize() 大小为原来的两倍
		- 元素重新映射, 树化/退化为链表
- TREEIFY_THRESHOLD 树化阈值
	- 默认 8, >=8 时
		- 哈希表长度<64, 扩容
		- 哈希表长度>=64, 树化
- UNTREEIFY_THRESHOLD 解除树化
	- 默认 6, <=6 时变成链表
- MIN_TREEIFY_CAPACITY 触发树化所需最小哈希表大小
	- 默认 64
	- 哈希表长度>=65 and 链表长度>8, 发生树化
## hash 方法
- 使用位运算(&)来代替取模运算(%)
	- `X % 2^n = X & (2^n – 1)`
- 对hashcode进行扰动计算
	- 把高位的特征和低位的特征组合起来, 降低哈希冲突的概率. 也就是说,尽量做到任何一位的变化都能对最终得到的结果产生影响.
## put 方法, key == null
放在数组第一个元素, 下标为 0
## 1.7 vs 1.8
[HashMap 1.7和1.8的区别 --答到面试官怀疑人生](https://blog.csdn.net/weixin_44141495/article/details/108402128)
- 1.8 使用了红黑树
- 插入顺序
	- 1.7 头插法 ([扩容时死循环](https://www.cnblogs.com/crazymakercircle/p/18060176#autoid-h3-1-2-0))
	- 1.8 尾插法
## See also
- [链表转红黑树的阈值为什么是8？红黑树转链表为什么是6？](https://blog.csdn.net/G_whang/article/details/113855384)
- [Java 8系列之重新认识HashMap](https://tech.meituan.com/2016/06/24/java-hashmap.html)
- [HashMap 源码解析](https://pdai.tech/md/java/collection/java-map-HashMap&HashSet.html)