# Java8 新特性
Java 8是Java自Java 5（发布于2004年）之后的最重要的版本。这里我们罗列了Java8中的核心新特性：

- Iterable接口中的forEach()方法
- 接口中的默认和静态方法
- 函数式接口与Lambda表达式
- JavaStreamAPI
- Java Time API
- Collection API 改进
- Concurrency API 改进
- Java IO 改进
- 其它各类 Core API 改进

## Iterable接口中的forEach()方法
Java 8在java.lang.Iterable接口中引入了forEach方法，因此在编写代码时，我们只需关注业务逻辑。forEach方法使用java.util.Function.ConsumerObject作为参数，因此它有助于将业务逻辑放在可以重用的单独位置。
```
// Iterable接口中的forEach方法
default void forEach(Consumer<? super T> action) {
    Objects.requireNonNull(action);
    for (T t : this) {
        action.accept(t);
    }
}
```
```
// forEach方法示例--lambda
items.forEach((k,v)->{
    System.out.println("Key : " + k + " Value : " + v);
});

// forEach方法示例--匿名类
items.forEach(new Consumer<Integer>() {
    public void accept(Integer t) {
        System.out.println("forEach anonymous class Value::"+t);
    }
});
// forEach方法示例--Consumer接口实现
MyConsumer action = new MyConsumer();
items.forEach(action);
```
## 接口中的默认和静态方法
Java 8对接口进行了增强，允许接口具有已经实现了的方法。我们可以使用default 和static 关键字来创建带实现方法的接口。例如Iterable接口中的forEach方法便是一个默认方法

```
// Iterable接口中的forEach方法
default void forEach(Consumer<? super T> action) {
    Objects.requireNonNull(action);
    for (T t : this) {
        action.accept(t);
    }
}
```
默认方法不能覆盖java.lang.Object中的方法。

如果层次结构中的任何类都有一个具有相同签名的方法，那么默认方法就变得不相关了。因为实现接口的任何类都已经将对象作为超类来实现，如果我们在接口中使用了equals()、hashCode()默认方法，那么它将变得无关紧要。这就是为什么为了更清晰起见，不允许接口拥有对象类默认方法。

在 java8 中的接口中不仅增加了默认方法，还增加了静态方法。使用方式接口名.方法名。
```
public interface Foo {
    static void bar(){  
 
        System.out.println("i'am static f");
    }
}

Foo.bar();// 直接使用
```

## 函数式接口与Lambda表达式
### 函数式接口
函数式接口是Java 8中引入的新概念。
- 接口有且仅有一个抽象方法
- 允许定义静态方法
- 允许定义默认方法
- 允许java.lang.Object中的public方法
- @FunctionInterface注解不是必须的，如果一个接口符合"函数式接口"定义，那么加不加该注解都没有影响。加上该注解能够更好地让编译器进行检查。如果编写的不是函数式接口，但是加上了@FunctionInterface，那么编译器会报错

```
// 正确的函数式接口
@FunctionalInterface
public interface FooInterface {
    // 有且仅有一个抽象方法
    public void sub();
 
    // java.lang.Object中的public方法
    public boolean equals(Object var1);
 
    // 默认方法
    public default void defaultMethod(){
    
    }
 
    // 静态方法
    public static void staticMethod(){
 
    }
}
```
java.util.function包里的函数式接口<sup>[2]</sup>
| 接口 | 参数 | 默认方法 | 定义示例 | 调用示例 |
| ---- | ---- | ---- | ---- | ---- |
| Function<T, R> | T：入参类型，R：出参类型 | R apply(T t) | Function<Integer, Integer> func = p -> p * 10 | func.apply(10) |
| Consumer<T> | T：入参类型；没有出参 | void accept(T t) | Consumer<String> consumer= p -> System.out.println(p) | consumer.accept("hi") |
| Supplier<T> | T：出参类型；没有入参 | T get() | Supplier<Integer> supplier= () -> 100 | supplier.get() |
| Predicate<T> | T：入参类型；出参类型是Boolean | boolean test(T t) | Predicate<Integer> predicate = p -> p % 2 == 0 | predicate.test(100) |

## lambda表达式<sup>[3]</sup>
lambda表达式是一段可以传递的代码，它的核心思想是将面向对象中的传递数据变成传递行为。 

我们回顾一下在使用java8之前要做的事，之前我们编写一个线程时是这样的：
```
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("do something.");      
    }
}
```
也有人会写一个类去实现Runnable接口。

这里采用匿名内部类的目的，就是为了方便 Java 程序员将代码作为数据传递。不过，匿名内部 类还是不够简便。 为了执行一个简单的任务逻辑，不得不加上 6 行冗繁的样板代码。那如果是lambda该怎么做?
```
Runnable r = () -> System.out.println("do something.");
```

在lambda中我们遵循如下的表达式来编写：
```
expression = (variable,...) -> action
```
- variable: 这是一个变量,一个占位符。像x,y,z,可以是多个变量；
- action: 这里我称它为action, 这是我们实现的代码逻辑部分,它可以是一行代码也可以是一个代码片段。
  
lambda表达式的类型为函数式接口。

## Java Stream API
Java 8中添加了一个新的java.util.Stream，用于对集合执行filter/map/reduce类似的操作。Stream API允许顺序执行和并行执行。
```
// 顺序执行
Stream<Integer> sequentialStream = items.stream();

// 并行执行
Stream<Integer> parallelStream = items.parallelStream();

// 将lambda与Stream API配合使用
Stream<Integer> highNums = parallelStream.filter(p -> p > 90);
// 将lambda与forEach配合使用
highNums.forEach(p -> System.out.println("High Nums parallel="+p));

Stream<Integer> highNumsSeq = sequentialStream.filter(p -> p > 90);
highNumsSeq.forEach(p -> System.out.println("High Nums sequential="+p));
```

StreamAPI对比collections特点<sup>[4]</sup>：
- 无存储。stream不是一种数据结构，它只是某种数据源的一个视图，数据源可以是一个数组，Java容器或I/O channel等。
- 为函数式编程而生。对stream的任何修改都不会修改背后的数据源，比如对stream执行过滤操作并不会删除被过滤的元素，而是会产生一个不包含被过滤元素的新stream。
- 惰式执行。stream上的操作并不会立即执行，只有等到用户真正需要结果的时候才会执行。
- 可消费性。stream只能被“消费”一次，一旦遍历过就会失效，就像容器的迭代器那样，想要再次遍历必须重新生成。

对stream的操作分为为两类，中间操作(intermediate operations)和结束操作(terminal operations)，二者特点是：
1. 中间操作总是会惰式执行，调用中间操作只会生成一个标记了该操作的新stream，仅此而已。
2. 结束操作会触发实际计算，计算发生时会把所有中间操作积攒的操作以pipeline的方式执行，这样可以减少迭代次数。计算完成之后stream就会失效。

| 操作类型 | 接口方法 |
| ---- | ---- |
| 中间操作 | concat() distinct() filter() flatMap() limit() map() peek() skip() sorted() parallel() sequential() unordered() |
| 结束操作 | allMatch() anyMatch() collect() count() findAny() findFirst() forEach() forEachOrdered() max() min() noneMatch() reduce() toArray() |

## Java Time API<sup>[5]</sup>
在新的时间API中，Instant表示一个精确的时间点，Duration和Period表示两个时间点之间的时间量。 
LocalDate表示日期，即xx年xx月xx日，即不包括时间也不带时区。LocalTime与LocalDate类似， 
但只包含时间。LocalDateTime则包含日期和时间。ZoneDateTime表示一个带时区的时间。 
DateTimeFormatter提供格式化和解析功能。下面详细的介绍使用方法。

## Collection API 改进
- Iterator默认方法用于对每个剩余元素执行给定的操作，直到处理完所有元素或抛出异常为止。
- 集合默认方法Removeif(谓词过滤器)，用于删除该集合中满足给定谓词的所有元素。
- 方法返回Spliterator实例，该实例可用于依次或并行地遍历元素。
- map replaceAll()、Compute()、Merge()方法。
- HashMap类的性能改进（键值冲突）

## Concurrency API 改进
- ConcurrentHashMap compute(), forEach(), forEachEntry(), forEachKey(), forEachValue(), merge(), reduce(), search() 方法.
- 可以显式完成的CompleetableFuture(设置其值和状态).
- Executors newWorkStealingPool() method to create a work-stealing thread pool using all available processors as its target parallelism level.

## Java IO 改进
- Files.list(Path dir)
- Files.lines(Path path)
- Files.find() 
- BufferedReader.lines()

## 其它核心API的改进
略


## 参考
- [1] [Java 8 新特性](http://w2class.com/java-8-features-with-examples/)
- [2] [java8 四大核心函数式接口Function、Consumer、Supplier、Predicate](https://blog.csdn.net/a879611951/article/details/80104014)
- [3] [Java8新特性--Lambda表达式](https://www.cnblogs.com/kexianting/p/8588987.html)
- [4] [Java Stream API入门篇](https://www.cnblogs.com/CarpenterLee/p/6545321.html)
- [5] [Java8日期和时间API](https://blog.csdn.net/a80596890555/article/details/58687444)