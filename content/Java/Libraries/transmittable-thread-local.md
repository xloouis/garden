---
tags:
  - java
  - ThreadLocal
---
使用了 wrapper 包装器模式包装了 `run` 方法

- [ttl](https://github.com/alibaba/transmittable-thread-local)
- [CRR](https://github.com/alibaba/transmittable-thread-local/blob/master/docs/developer-guide.md#-框架中间件集成ttl传递)
- [解析文章](https://github.com/alibaba/transmittable-thread-local/issues/123)

[![时序图](https://user-images.githubusercontent.com/1063891/233595980-ef7f1f8b-36cd-45b3-b55b-45f7b3d1c94f.png)](https://github.com/alibaba/transmittable-thread-local#dummy)
## 为什么要 restore

关注的是 **上下文传递流程的规范化**。上下文传递到了子线程要做好 **_清理_**（或更准确地说是要 **_恢复_** 成之前的上下文），需要业务逻辑去处理好。如果业务逻辑对**清理**的处理不正确，比如：

- 如果清理操作漏了：
    - 下一次执行可能是上次的，即『上下文的 **_污染_**/**_串号_**』，会导致业务逻辑错误。
    - 『上下文的 **_泄漏_**』，会导致内存泄漏问题。
- 如果清理操作做多了，会出现上下文 **_丢失_**。

上面的问题，在业务开发中引发的`Bug`真是**屡见不鲜** ！本质原因是：**_`ThreadLocal`的`set/remove`的上下文传递模式_** 在使用线程池等异步执行组件的情况下不再是有效的。常见的典型例子：

- 当线程池满了且线程池的`RejectedExecutionHandler`使用的是`CallerRunsPolicy`时，提交到线程池的任务会在提交线程中直接执行，`ThreadLocal.remove`操作**清理**提交线程的上下文导致上下文**丢失**。
- 类似的，使用`ForkJoinPool`（包含并行执行`Stream`与`CompletableFuture`，底层使用`ForkJoinPool`）的场景，展开的`ForkJoinTask`会在任务提交线程中直接执行。同样导致上下文**丢失**。

怎么设计一个『上下文传递流程』方案（即上下文的生命周期），以**保证**没有上面的问题？

期望：上下文生命周期的操作从业务逻辑中分离出来。业务逻辑不涉及生命周期，就不会有业务代码如疏忽清理而引发的问题了。整个上下文的传递流程或说生命周期可以规范化成：捕捉、回放和恢复这3个操作，即[**_`CRR(capture/replay/restore)`模式_**](https://github.com/alibaba/transmittable-thread-local/blob/master/docs/developer-guide.md#-%E6%A1%86%E6%9E%B6%E4%B8%AD%E9%97%B4%E4%BB%B6%E9%9B%86%E6%88%90ttl%E4%BC%A0%E9%80%92)。更多讨论参见 [Issue：能在详细讲解一下`replay`、`restore`的设计理念吗？#201](https://github.com/alibaba/transmittable-thread-local/issues/201)。

## See Also

- [CRR操作](https://github.com/alibaba/transmittable-thread-local/issues/201)
- [调用链跨线程传递ThreadLocal对象](https://tech.kujiale.com/diao-yong-lian-kua-xian-cheng-chuan-di-threadlocaldui-xiang/)
