# 迭代

## Iterator

迭代器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作。

Iterator 的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是 ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费。

### Iterator接口

Iterator 接口的目的，就是为所有数据结构，提供了一种统一的访问机制，即`for...of`循环。

ES6 规定，默认的 Iterator 接口部署在数据结构的`Symbol.iterator`属性。如果`Symbol.iterator`方法对应的不是遍历器生成函数（即会返回一个遍历器对象），解释引擎将会报错。

凡是部署了`Symbol.iterator`属性的数据结构，就称为可遍历对象。调用这个接口，就会返回一个遍历器对象。

可遍历对象使用方括号语法执行Symbol.iterator方法，会获得一个iterator对象，iterator可以用next方法获取一个IteratorResult对象。该对象有两个属性，value和done，分别表示迭代的下一个元素和迭代是否结束。

```typescript
interface Iterable {
  [Symbol.iterator]() : Iterator,
}

interface Iterator {
  next(value?: any) : IterationResult,
}

interface IterationResult {
  value: any,
  done: boolean,
}
```

原生具备 Iterator 接口的数据结构如下。

- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象

#### 使用场合

1. 解构赋值
2. 拓展运算符
3. yield *
4. 数组的遍历会调用遍历器接口，所以任何接受数组作为参数的场合，其实都调用了遍历器接口。
   * for...of
   * Array.from()
   * Map(), Set(), WeakMap(), WeakSet()（比如`new Map([['a',1],['b',2]])`）
   * Promise.all()
   * Promise.race()

#### 遍历器对象

遍历器对象除了具有`next()`方法，还可以具有`return()`方法和`throw()`方法。

`return()`方法的使用场合是，如果`for...of`循环提前退出（通常是因为出错，或者有`break`语句），就会调用`return()`方法。如果一个对象在完成遍历前，需要清理或释放资源，就可以部署`return()`方法。

`throw()`方法主要是配合 Generator 函数使用，一般的遍历器对象用不到这个方法。

### for...of 循环

ES6 引入了`for...of`循环，作为遍历所有数据结构的统一的方法。

`for...of`循环内部调用的是数据结构的`Symbol.iterator`方法。

#### 数组

数组原生具备`iterator`接口，`for...of`循环可以代替数组实例的`forEach`方法。

`for...in`循环读取键名，`for...of`循环读取键值。如果要通过`for...of`循环，获取数组的索引，可以借助数组实例的`entries`方法和`keys`方法。

#### Set, Map

Set 和 Map 结构也原生具有 Iterator 接口，可以直接使用`for...of`循环。

* 遍历的顺序是按照各个成员被添加进数据结构的顺序。
* Set 结构遍历时，返回的是一个值，而 Map 结构遍历时，返回的是一个数组，该数组的两个成员分别为当前 Map 成员的键名和键值。

#### 字符串

`for...of`循环遍历字符串，会正确识别 32 位 UTF-16 字符。

#### Generator

`for...of`循环可以自动遍历 Generator 函数运行时生成的`Iterator`对象，且此时不再需要调用`next`方法。一旦`next`方法的返回对象的`done`属性为`true`，`for...of`循环就会中止。

## 异步遍历器

Iterator 协议里面`next()`方法只能包含同步操作。一旦执行`it.next()`方法，就必须同步地得到`value`和`done`这两个属性。

目前的解决方法是，将异步操作包装成 Thunk 函数或者 Promise 对象，即`next()`方法返回值的`value`属性是一个 Thunk 函数或者 Promise 对象，等待以后返回真正的值，而`done`属性则还是同步产生的。

ES2018 [引入](https://github.com/tc39/proposal-async-iteration)了“异步遍历器”（Async Iterator），为异步操作提供原生的遍历器接口，即`value`和`done`这两个属性都是异步产生。

### 异步遍历器接口

异步遍历器的最大的语法特点，就是调用遍历器的`next`方法，返回的是一个 Promise 对象。

对象的异步遍历器接口，部署在`Symbol.asyncIterator`属性上面。

异步遍历器的设计目的之一，就是 Generator 函数处理同步操作和异步操作时，能够使用同一套接口。

### for await ... of

新引入的`for await...of`循环，则是用于遍历异步的 Iterator 接口,也可以用于同步遍历器。。

`for...of`循环自动调用异步遍历器的`next`方法，会得到一个 Promise 对象。`await`用来处理这个 Promise 对象，一旦`resolve`，就把得到的值（`x`）传入`for...of`的循环体。

`for await...of`循环的一个用途，是部署了 asyncIterable 操作的异步接口，可以直接放入这个循环。

如果`next`方法返回的 Promise 对象被`reject`，`for await...of`就会报错，要用`try...catch`捕捉。

### 异步 Generator 函数

异步 Generator 函数的作用，是返回一个异步遍历器对象。

在语法上，异步 Generator 函数就是`async`函数与 Generator 函数的结合。

异步 Generator 函数内部，能够同时使用`await`和`yield`命令。可以这样理解，`await`命令用于将外部操作产生的值输入函数内部，`yield`命令用于将函数内部的值输出。

## Generator

Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。

Generator 函数实现了Iterator接口。执行 Generator 函数会返回一个遍历器对象，返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

ES6 规定这个遍历器对象是 Generator 函数的实例，也继承了 Generator 函数的`prototype`对象上的方法。Generator 函数不能跟`new`命令一起用，会报错。

Generator 函数是一个普通函数，有两个特征。

1. `function`关键字与函数名之间有一个星号
2. 函数体内部使用`yield`表达式，定义不同的内部状态

如果一个对象的属性是 Generator 函数，可以简写成下面的形式。

```javascript
let obj = {
  * myGeneratorMethod() {
    ···
  }
};
```

### yield 表达式

由于 Generator 函数返回的遍历器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`表达式就是暂停标志。

* `yield`表达式后面的表达式，只有当调用`next`方法、内部指针指向该语句时才会执行
* `yield`表达式只能用在 Generator 函数里面，用在其他地方都会报错。
* `yield`表达式如果用在另一个表达式之中，必须放在圆括号里面。

#### return、yield辨析

return、yield都能返回紧跟在语句后面的那个表达式的值。

区别

1. 每次遇到`yield`，函数暂停执行，下一次再从该位置继续向后执行，而`return`语句不具备位置记忆的功能
2. 正常函数只能返回一个值，因为只能执行一次`return`；Generator 函数可以返回一系列的值，因为可以有任意多个`yield`。

#### yield* 表达式

yield* 用来在一个 Generator 函数里面执行另一个 Generator 函数。

如果`yield`表达式后面跟的是一个遍历器对象，需要在`yield`表达式后面加上星号，表明它返回的是一个遍历器对象。这被称为`yield*`表达式。

* 如果`yield*`后面跟着一个数组，由于数组原生支持遍历器，因此就会遍历数组成员。
* 任何数据结构只要有 Iterator 接口，就可以被`yield*`遍历。
* 如果被代理的 Generator 函数有`return`语句，那么就可以向代理它的 Generator 函数返回数据。

### 遍历器对象

遍历器对象有三个方法，`next()`、`throw()`、`return()`，它们的作用都是让 Generator 函数恢复执行，并且使用不同的语句替换`yield`表达式。

* `next()`是将`yield`表达式替换成一个值。
* `throw()`是将`yield`表达式替换成一个`throw`语句。
* `return()`是将`yield`表达式替换成一个`return`语句。

#### next 方法

遍历器对象的`next`方法的运行逻辑如下。

（1）遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。

（2）下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。

（3）如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。

（4）如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

##### 参数

`yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。

Generator 函数从暂停状态到恢复运行，它的上下文状态（context）是不变的。通过`next`方法的参数，就有办法在 Generator 函数开始运行之后，继续向函数体内部注入值，从而调整函数行为。

* 由于`next`方法的参数表示上一个`yield`表达式的返回值，所以在第一次使用`next`方法时，传递参数是无效的。

#### throw方法

Generator 函数返回的遍历器对象，都有一个`throw`方法，可以在函数体外抛出错误，然后在 Generator 函数体内捕获。

* 如果Generator 函数内部没有部署`try...catch`代码块，throw抛出的错误会传递到外部
* 如果 Generator 函数内部和外部，都没有部署`try...catch`代码块，调用throw方法后程序将报错，直接中断执行。
* `throw`方法抛出的错误要被内部捕获，前提是必须至少执行过一次`next`方法。
* `throw`方法被捕获以后，会附带执行下一条`yield`表达式。

#### return方法

`return()`方法，可以返回给定的值，并且终结遍历 Generator 函数。

调用`return()`方法后，返回值的`value`属性就是`return()`方法的参数，`done`为true。如果`return()`方法调用时，不提供参数，则返回值的`value`属性为`undefined`。