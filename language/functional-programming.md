# 函数式编程
## 1. 引言
"函数式编程"是一种"编程范式"（programming paradigm），也就是如何编写程序的方法论。
它属于"结构化编程"的一种，主要思想是把运算过程尽量写成一系列嵌套的函数调用。它将计算看作数学函数的求值，避免了状态的变化和数据的变化。它是一种声明式编程范式，这意味着编程是用表达式(expressions)或声明(declarations)完成的而不是语句(statements)。

函数式编程的代码是幂等的，函数的输出值仅取决于传递给函数的参数，因此每次用相同的值对参数x调用函数f产生相同的结果f(X)；这与依赖于本地或全局状态的过程procedures形成了对比，procedures在调用相同的参数但程序状态不同时，可能会产生不同的结果。

## 2. 命令式编程

## 3. 特点<sup>[3]</sup>
### a. 头等和高阶函数First-class and higher-order functions
first-class functions：如果一种编程语言把函数看作是头等的公民，那么它就被称为具有头等的函数 。这意味着语言支持将函数作为参数传递给其他函数，将它们作为来自其他函数的值返回，并将它们赋值给变量或将它们存储在数据结构中。“first-class”是一个计算机科学术语，用来描述对其自身没有限制的编程语言实体。

higher-order functions：高阶函数是这样一个函数：以一个或多个函数作为参数(即过程参数)；返回一个函数作为其结果。高阶是一个数学概念，主要描述了一个函数在其它函数上进行操作这一数学概念。

### b. 纯函数，没有"副作用"
只用表达式expression，不用语句statement

"表达式"（expression）是一个单纯的运算过程，总是有返回值；"语句"（statement）是执行某种操作，没有返回值。函数式编程要求，只使用表达式，不使用语句。也就是说，每一步都是单纯的运算，而且都有返回值。
实际应用中，不做I/O是不可能的。因此，编程过程中，函数式编程只要求把I/O限制到最小，不要有不必要的读写行为，保持计算过程的单纯性。

所谓"副作用"（side effect），指的是函数内部与外部互动（最典型的情况，就是修改全局变量的值），产生运算以外的其他结果。

### c. 引用透明Referential transparency
函数式程序没有赋值语句，也就是说，函数式程序中的变量的值一旦定义就不会改变。这也导致了在任何执行点都可以用变量的（初始）值替换变量，反之亦然。这就是引用透明。


## 4. 优点<sup>[3]</sup>
- 代码简介
- 接近自然语言
```
add(1,2).multiply(3).subtract(4)
```
- 方便代码管理/测试
- 易于并发
```
函数式编程不需要考虑"死锁"（deadlock），因为它不修改变量，所以根本不存在"锁"线程的问题。不必担心一个线程的数据，被另一个线程修改，所以可以很放心地把工作分摊到多个线程，部署"并发编程"（concurrency）。
```
- 代码的热升级

## 5. 对比命令式编程
与函数式编程相对的是命令式编程（imperative programming）。命令式编程用源代码中的命令更改状态，最简单的例子是赋值。命令式编程确实有子例程函数，但它们不是数学意义上的函数。它们可能会产生副作用，从而改变程序状态的值，所以在命令式编程中没有返回值的函数是有意义的。也是因为这样，它们缺乏引用透明性（Referential transparency），即同一语言表达式在不同的时间会根据执行程序的状态而产生不同的值。

函数式编程和命令式编程两者最大的区别在于函数式编程避免各种对函数外部的副作用（side effect），而命令式编程欣然接受side effect以实现state和I/O。

## 6. Snippets <sup>[3]</sup>
下面是几个例子
```
// 命令式编程
var a = 1 + 2;
var b = a * 3;
var result = b - 4;
```
```
// 函数式编程
var result1 = subtract(multiply(add(1,2), 3), 4);
// or
var result2 = add(1,2).multiply(3).subtract(4)
```

## 参考
- [1] [Functional programming wiki](https://en.wikipedia.org/wiki/Functional_programming)
- [2] [Functional Programming For The Rest of Us](http://www.defmacro.org/ramblings/fp.html)
- [3] [函数式编程初探 - 阮一峰](http://www.ruanyifeng.com/blog/2012/04/functional_programming.html)
- [4] [头等函数](https://en.wikipedia.org/wiki/First-class_function)