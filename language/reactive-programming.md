# 响应式编程 Reactive Programming
响应式编程是一种异步编程范式，涉及数据流和变化的传播。
这意味着可以通过所使用的编程语言轻松地表示静态(例如数组)或动态(例如事件发射器)数据流。

作为向响应式编程方向迈出的第一步，Microsoft在.NET生态系统中创建了Reactive Extensions (Rx)库。然后，RxJava在JVM上实现了响应式编程。
随着时间的推移，Java的标准化通过响应式流(ReactiveStreams)出现了，这是一种规范，定义了JVM上的响应式库的一组接口和交互规则。
它的接口已经集成到Java 9中的Flow父类下。

响应式编程范例经常以观察者设计模式这样的扩展出现在面向对象的语言中。他与迭代器Iterator的主要的区别是，迭代器是基于拉(pull-based)的，而反应流是基于推送(push-based)的。

迭代器Iterator是一种命令式编程模式，虽然访问值的方法完全由Iterable负责，但实际上由开发人员来选择何时访问序列中的next()项。相反，在响应式流中，发布者Publisher在新的可用值出现时通知订阅者Subscriber，而这个push便是响应式的关键。此外，应用于推送值的操作是声明性的，而不是命令性的：程序员表示计算的逻辑，而不是描述其精确的控制流。具体可以参考[函数式编程](./functional-programming.md)。

除了push值之外，响应式编程也很好地定义了error和completion的处理方式。Publisher可以将新值推送到它的Subscriber(通过调用onNext)，也可以发出错误(通过调用onError)和完成(通过调用onComplete)信号。错误信号和完成信号均会终止序列。

## 背景


## 参考
- [1] [Reactive Programming Wiki](https://en.wikipedia.org/wiki/Reactive_programming)
- [2] [Reactor reference](https://projectreactor.io/docs/core/release/reference/#intro-reactive)