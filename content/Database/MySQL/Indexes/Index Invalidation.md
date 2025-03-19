---
title: 索引失效
tags:
    - mysql
    - index
---

索引失效的情况

- 不等于（!= 或者<>）索引失效
- is null 可以使用索引, is not null 无法使用索引
- 左模糊, 或全模糊的 `like` 匹配
    - 左模糊可以通过加一个镜像字段转化成右模糊
    - 如果数据库表中的字段只有主键+二级索引, 那么即使使用了左/全模糊匹配，也不会走全表扫描（type=all），而是走全扫描二级索引树(type=index), 这个时候就是索引覆盖了
- 对索引做了表达式计算; 使用了函数
    - 可以使用函数索引
- 索引列比较的时候发生隐式类型转换
    - 在遇到字符串和数字比较的时候，会自动把字符串转为数字，然后再进行比较
- 联合索引没有满足最左匹配
- where 里面存在 or, 只要有条件列不是索引列, 就会失效
- 具体还要看explain的输出

## See Also

- [谁还没碰过索引失效呢?](https://mp.weixin.qq.com/s/lEx6iRRP3MbwJ82Xwp675w)
- [索引失效有哪些](https://www.xiaolincoding.com/mysql/index/index_lose.html#%E7%B4%A2%E5%BC%95%E5%A4%B1%E6%95%88%E6%9C%89%E5%93%AA%E4%BA%9B)
