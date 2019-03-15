# Reactor
Reactor是一个完全非阻塞的JVM响应式编程基础。Reactor直接集成了Java8的函数式接口：CompletableFuture, Stream和 Duration，提供了可合成的异步序列接口Flux（用于多个元素）和Mono（用于0或一个元素），广泛地实现了[Reactive Streams](http://www.reactive-streams.org/) 规范。

其中，reactor-netty 项目亦支持非阻塞进程间通信，提供了支持背压的HTTP,TCP,UDP网络引擎。

## 核心特征
## 操作符
在Reactor里，operator操作符往Publisher上添加行为并且封装前一步的Publisher进一个新的实例。如此数据产生自第一个Publisher并且从链往下流转。最后，Subscriber 来最后完结这一过程。在这里，nothing happens until a Subscriber subscribes to a Publisher。

下表列举了部分操作符<sup>[2]</sup>
| 操作符 | 返回 | 作用描述 |
| ---- | ---- | ---- |
| map | Flux<V> | 对序列中给的每项应用输入的Function进行转换 |
| flatMap | Flux<V> | 把一个发射器通过某种方法转换为多个，然后再把这些分散的Publisher装进一个单一的发射器Flux。 |
| next | Mono<T> | 发射Flux中的第一项到一个Mono中 |
| merge | Mono<T> | 合并多个publisher里序列的数据 |
| fliter | Flux<V> | 使用断言Predicate评价序列中的每一项，发射测试通过的项 |
| all | Mono<V> | 当序列中的所有项均符合断言Predicate的时候发射一个true |
| any | Mono<V> | 当序列中的任一项符合断言Predicate的时候发射一个true |
| onBackpressureBuffer | Flux<T> | Request an unbounded demand and push to the returned Flux, or park the observed elements if not enough demand is requested downstream. 使用缓冲区来缓冲上游信号，缓冲的机制有多种具体视方法。 |

### Flux
![avatar](https://raw.githubusercontent.com/reactor/reactor-core/v3.0.7.RELEASE/src/docs/marble/flux.png)

Flux<T>对象是一个标准的Publisher<T>对象，表示一个可选地被完成信号或者错误信号所终止的含有0到N个发射项的异步序列。正如Reactive Streams里的规范所描述的，这3个类型的信号（或事件）将会调用下游的订阅者Subscriber的 onNext, onComplete 或 onError 方法。

上述3类事件均是可的：没有onNext但是有onComplete 事件表示一个空的优先序列，同时当移除onComplete 后将得到一个无限空序列。同样的，无限序列并不一定需要为空，例如，Flux.interval(Duration)方法能够创建一个Flux<Long>对象，该对象是无限的且能重复发射出ticks。

### Mono
![avatar](https://raw.githubusercontent.com/reactor/reactor-core/v3.0.7.RELEASE/src/docs/marble/mono.png)

Mono<T>对象是一个特殊的Publisher<T>对象，它至多发射一个发射项，然后可选地被onComplete或者onError信号所终止。

Mono<Void>可以被用来表示无值的只存在完成概念的异步过程（类似Runnable）。

### 快速创建和订阅
使用列表创建
```
// 创建一个发射所输入序列的Flux并且complete
Flux<String> seq1 = Flux.just("foo", "bar", "foobar");

List<String> iterable = Arrays.asList("foo", "bar", "foobar");
Flux<String> seq2 = Flux.fromIterable(iterable);
```
工厂方法创建
```
Mono<String> noData = Mono.empty(); 

Mono<String> data = Mono.just("foo");

Flux<Integer> numbersFromFiveToSeven = Flux.range(5, 3);
```
使用Java8 lambdas进行订阅
```
subscribe(); 

subscribe(Consumer<? super T> consumer); 

subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer); 

subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer,
          Runnable completeConsumer); 

subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer,
          Runnable completeConsumer,
          Consumer<? super Subscription> subscriptionConsumer);
```
以上的方法均会返回对订阅的一个引用Disposable ，我们可用通过这个引用来取消订阅。一旦被取消，源将会停止产生数据以及清理它所产生的所有资源。

#### 订阅代码样例
```
Flux<Integer> ints = Flux.range(1, 3); 
ints.subscribe()
```
```
Flux<Integer> ints = Flux.range(1, 3); 
ints.subscribe(i -> System.out.println(i));
>> 输出
# 1
# 2
# 3
```
```
Flux<Integer> ints = Flux.range(1, 4) 
      .map(i -> { 
        if (i <= 3) return i; 
        throw new RuntimeException("Got to 4"); 
      });
ints.subscribe(i -> System.out.println(i), 
      error -> System.err.println("Error: " + error));
>> 输出
# 1
# 2
# 3
# Error: java.lang.RuntimeException: Got to 4
```
```
Flux<Integer> ints = Flux.range(1, 4); 
ints.subscribe(i -> System.out.println(i),
    error -> System.err.println("Error " + error),
    () -> System.out.println("Done"));
>> 输出
# 1
# 2
# 3
# 4
# Done
```
### 线程Threading和调度器Scheduler
在Reactor中，执行模型以及在哪执行是由采用的Scheduler调度器决定的。Scheduler具有类似ExecutorService的调度责任，以及一些列专有的抽象。

Scheduler的静态方法
- 当前线程 Schedulers.immediate()
- 单一可复用线程 Schedulers.single()
- 单一不复用线程 Schedulers.newSingle() 
- 弹性线程池 Schedulers.elastic()
- 固定的worker池Schedulers.parallel()，数目由cpu核数决定
- Schedulers.fromExecutorService(ExecutorService)

某些操作符会默认使用Schedulers 里的调度器，例如 Flux.interval(Duration.ofMillis(300))会每300ms产生一个Flux<Long>，默认情况下使用的是Schedulers.parallel()。

Reactor提供了两种在响应式链中切换执行上下文（Scheduler）的方法：publishOn 和subscribeOn。两者均通过接受一个Scheduler 对象来将执行上下文切换至其上。

题外话：在Reactor中，当你串起来一堆的操作符的时候，你可以封装任意多的Flux和Mono实现一个包着一个。而当你订阅的时候，Reactor会创建一个Subscriber对象链，从尾到第一个publisher。

#### publishOn
影响调用之后的操作执行上下文
```
Scheduler s = Schedulers.newParallel("parallel-scheduler", 4);  //(1)

final Flux<String> flux = Flux
    .range(1, 2)
    .map(i -> 10 + i)  //(2)
    .publishOn(s)  //(3)
    .map(i -> "value " + i);  //(4)

new Thread(() -> flux.subscribe(System.out::println));//（5）
```
- (1) 创建一个4线程Scheduler
- (2) 第一个map运行在5的匿名线程上
- (3) publishOn 切换整个序列到<1>上的一个线程
- (4) 第二个map运行在<1>上的一个线程
- (5) 在这个匿名线程上发生了订阅。但是print发生在最后publishOn的线程。
  
#### subscribeOn
影响发射源的上下文

## 参考
- [1] [projectreactor官网](https://projectreactor.io)
- [2] [Reactor操作符](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#all-java.util.function.Predicate-)