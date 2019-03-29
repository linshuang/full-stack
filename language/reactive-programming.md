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
各种应用场景对当前的软硬件提出了更高的性能要求，关于软件方面的性能提升宽泛地讲主要包含两个途径：
- 并行：使用更多线程和更多硬件资源
- 更加有效地利用已有的资源

通常，开发者（例如Java的）使用阻塞代码编写程序。在出现性能瓶颈后，开发者便引入额外的线程，运行这样的阻塞代码。但是这种扩展会很快带来资源的争用和并发问题。更糟糕的是，阻塞代码仍旧在浪费资源。例如，当程序出现一些延迟(特别是I/O，例如数据库请求或网络调用)时，资源就会被浪费，因为线程(或许多线程)现在处于空闲状态，等待数据。

## 使用异步技术
通过编写异步、非阻塞代码，可以让执行切换到使用相同底层资源的另一个活动任务，然后在异步处理完成后返回到当前进程。

在JVM上我们可以用Callback和Future编码。
```
// Callback代码样例
userService.getFavorites(userId, new Callback<List<String>>() { 
  public void onSuccess(List<String> list) { 
      suggestionService.getSuggestions(new Callback<List<Favorite>>() {
        public void onSuccess(List<Favorite> list) { 
          UiUtils.submitOnUiThread(() -> { 
            list.stream()
                .limit(5)
                .forEach(uiList::show); 
            });
        }

        public void onError(Throwable error) { 
          UiUtils.errorPopup(error);
        }
      });
  }

  public void onError(Throwable error) {
    UiUtils.errorPopup(error);
  }
});
```
```
// Future代码样例
CompletableFuture<List<String>> ids = ifhIds(); 

CompletableFuture<List<String>> result = ids.thenComposeAsync(l -> { 
	Stream<CompletableFuture<String>> zip =
			l.stream().map(i -> { 
				CompletableFuture<String> nameTask = ifhName(i); 
				CompletableFuture<Integer> statTask = ifhStat(i); 

				return nameTask.thenCombineAsync(statTask, (name, stat) -> "Name " + name + " has stats " + stat); 
			});
	List<CompletableFuture<String>> combinationList = zip.collect(Collectors.toList()); 
	CompletableFuture<String>[] combinationArray = combinationList.toArray(new CompletableFuture[combinationList.size()]);

	CompletableFuture<Void> allDone = CompletableFuture.allOf(combinationArray); 
	return allDone.thenApply(v -> combinationList.stream()
			.map(CompletableFuture::join) 
			.collect(Collectors.toList()));
});

List<String> results = result.join();
```
而reactor代码则会易读很多
```
userService.getFavorites(userId) 
           .flatMap(favoriteService::getDetails) 
           .switchIfEmpty(suggestionService.getSuggestions()) 
           .take(5) 
           .publishOn(UiUtils.uiThreadScheduler()) 
           .subscribe(uiList::show, UiUtils::errorPopup); 

```

## 响应式编程特点
- 可组合性和可读性
- 使用丰富的操作符(operators)操作的数据流
- 在你订阅(subscribe)之前什么都不会发生
- 背压或消费者向生产者发出排放率过高的信号的能力
- 高层次但高价值的抽象，与并发无关

## 参考
- [1] [Reactive Programming Wiki](https://en.wikipedia.org/wiki/Reactive_programming)
- [2] [Reactor(java) reference](https://projectreactor.io/docs/core/release/reference/#intro-reactive)