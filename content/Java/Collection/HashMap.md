---
draft: true
tags:
  - java
  - hashmap
---
## 数据结构
- 拉链法(链地址法)
- 1.8 使用红黑树
## 参数
### capacity
- 默认 16
- 必须是 2^n, 找到比传入的指定值大的第一个2的幂

Java7 是先扩容后插入新值的，Java8 先插值再扩容，不过这个不重要。
## hash 方法
- 使用位运算(&)来代替取模运算(%)
	- `X % 2^n = X & (2^n – 1)`
- 对hashcode进行扰动计算
	- 把高位的特征和低位的特征组合起来, 降低哈希冲突的概率. 也就是说,尽量做到任何一位的变化都能对最终得到的结果产生影响.
## See also
- [HashMap 源码解析](https://pdai.tech/md/java/collection/java-map-HashMap&HashSet.html)