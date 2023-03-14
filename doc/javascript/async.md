# 异步编程

 JavaScript 里的异步方案演进：

Raw Callback Style -> Promise Callback Style -> Generator Callback Style -> Async/Await Callback

* Raw Callback Style: 朴素函数作为 callback，接受 error, data 等参数
* Promise Callback Style：通过 { then } 对象，去处理 onFulfilled 和 onRejected 两个 callback 函数。
* Generator Callback Style：通过 * 号和 yield 关键字，将多个嵌套 callback 扁平化的语法糖。
* Async/Await Callback Style：通过 async 和 await 关键字，将 Promise + Generator Callback Style 语义化和标准化的产物。

演进的过程是异步处理数据能力减弱，对开发者越发友好的过程。

## 异步操作

### 单线程模型

单线程模型指的是，JavaScript 只在一个线程上运行。也就是说，JavaScript 同时只能执行一个任务，其他任务都必须在后面排队等待。

为了避免复杂性，JavaScript 一开始就是单线程，这已经成了这门语言的核心特征。

单线程中只要有一个任务耗时很长，后面的任务都必须排队等着，会拖延整个程序的执行。JavaScript使用异步解决这个问题。

为了利用多核 CPU 的计算能力，HTML5 提出 Web Worker 标准，允许 JavaScript 脚本创建多个线程，但是子线程完全受主线程控制，且不得操作 DOM。

* JavaScript 只在一个线程上运行，不代表 JavaScript 引擎只有一个线程。

### 任务

运行环境提供的API是以同步或异步模式运行。

> 如果调用方需要等待被调用方运行结束，那么称这种方法调用为同步。
>
> 如果调用方不需等待被调用方运行结束，那么称这种方法调用为异步。

#### 同步任务

同步任务是那些没有被引擎挂起、在主线程上排队执行的任务。

#### 异步任务

异步任务是那些被引擎放在一边，不进入主线程、而进入任务队列的任务。只有引擎认为某个异步任务可以执行了，该任务才会进入主线程执行。

### 异步模式

#### 回调函数

异步任务基于回调函数，回调函数是由调用者定义，交由执行者执行的函数。回调函数，是把任务的后续逻辑单独写在一个函数里面，等到重新执行这个任务的时候，就直接调用这个函数。

回调函数的优点是简单、容易理解和实现，缺点是不利于代码的阅读和维护，各个部分之间高度耦合。

##### 回调函数问题

没有Promise时，JavaScript异步操作通过回调函数实现。回调函数本身并没有问题，它的问题出现在多个回调函数嵌套。

回调函数存在着如下问题：

1. 如果异步操作存在依赖，则会形成嵌套的回调函数，导致代码不利于维护，这种情况被称为“回调地狱”。

	> 如果要进行操作A，需要先完成操作B，则称A依赖于B，A和B存在依赖。

1. 代码跳跃，不符合正常的思维习惯
2. 信任问题，如果把回调函数交给第三方库，不知道它会在什么时候怎么执行，如同一个黑盒
3. 第三方库可能没有提供错误处理
4. 不清楚回调是否是异步调用

#### 事件监听

异步任务的执行不取决于代码的顺序，而取决于某个事件是否发生。

这种方法的优点是比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，而且可以“[去耦合](https://en.wikipedia.org/wiki/Decoupling)”，有利于实现模块化。缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰。

## Promise异步方案

Promise是异步操作的一种解决方案。

`Promise`，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。

**缺点**

* 无法取消`Promise`，一旦新建它就会立即执行
* 如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部
* 当处于`pending`状态时，无法得知目前进展到哪一个阶段
* 最大问题是代码冗余，一眼看去都是一堆`then`，原来的语义变得很不清楚。

### 基本用法

#### 创建

ES6 规定，`Promise`对象是一个构造函数，用来生成`Promise`实例。

`Promise`构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`。

#### 状态

Promise有3种状态，分别是等待状态（pending）、完成状态（fulfiled）、拒绝状态（rejected）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。

Promise对象创建时是等待状态；在程序执行时，如果resolve被调用，则promise进入完成状态；如果reject被调用，则promise进入拒绝状态。

如果调用`resolve`函数和`reject`函数带有参数，那么它们的参数会被传递给回调函数。如果resolve传入promise，那么整体的状态由传入的promise决定。

如果改变已经发生了，再对`Promise`对象添加回调函数，也会立即得到这个结果。

Promise状态变化有如下规则：

* Promise的状态一旦变化，就不会改变。

* 对象的状态不受外界影响，只有resolve和reject能改变状态

  resolve和reject可以传递参数，then方法的回调函数可以接收到该参数。

#### 注意事项

* resolve或reject执行后的代码依旧可以执行
* 处理多个Promise的Promise构造函数方法
  * 接收的参数需要是可迭代的
  * 传入的数组元素如果不是Promise会转变成Promise对象
  * 可以对错误进行统一处理

#### 链式调用promise

promise可以链式调用，因而能够解决回调函数多重嵌套的问题。

当调用then方法后返回一个新的promise对象，可以在之后继续使用then方法处理该promise对象。

**错误捕获**

promise链中错误捕获有两种方式

1. 通过then方法的第二个回调函数对特定步骤的错误进行处理
2. 使用catch方法会捕获所有之前的promise未处理的错误

### promise方法

#### 静态方法

**单个Promise状态管理**

##### Promise.resolve()

成功状态Promise的一种简写形式。

```javascript
 new Promise(resolve=>resolve("success"));
 Promise.resolve("success");
```

参数

* 不带参数

  返回一个新的 Promise 对象，状态为`resolved`。

* 一般参数

  返回一个新的 Promise 对象，状态为`resolved`。

* Promise对象

  当Promise.resolve()接收Promise对象时，直接返回该对象

* 具有then方法的对象

  Promise.resolve()会调用传入对象的then方法，并传入resolve和reject参数，如果对象then方法使用resolve或reject，Promise对象的状态会发生改变，否则不变。

##### Promise.reject()

`Promise.reject(reason)`方法也会返回一个新的 Promise 实例，该实例的状态为`rejected`。

不管什么参数，都会向后传递，作为后续方法的参数。

**多个Promise状态管理**

##### Promise.all()

Promise.all可以传入多个Promise实例，包装成一个新的Promise实例返回，用来关注多个Promise的状态变化。

`Promise.all()`方法的参数可以不是数组，但必须具有 Iterator 接口，且返回的每个成员都是 Promise 实例。

如果传入的元素非Promise，会调用Promise.resolve方法，将其转化为Promise实例。

 Promise.all()的状态变化与所有传入的Promise 实例对象状态有关。所有状态都变成resolved，最终的状态才会变成resolved，将Promise实例的返回值组成数组，传递下去；只要有一个变成 rejected，最终的状态就变成rejected。

##### Promise.race()

`Promise.race()`方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。

Promise.race()的状态取决于第一个完成的Promise实例对象，如果第一个完成的成功了，那最终的状态就成功;如果第一个完成的失败了，那最终的状态就失败。

##### Promise.allSettled()

[ES2020](https://github.com/tc39/proposal-promise-allSettled) 引入了`Promise.allSettled()`方法，用来确定一组异步操作是否都结束了（不管成功或失败）。

`Promise.allSettled()`方法接受一个数组作为参数，数组的每个成员都是一个 Promise 对象，并返回一个新的 Promise 对象。只有等到参数数组的所有 Promise 对象都发生状态变更（不管是`fulfilled`还是`rejected`），返回的 Promise 对象才会发生状态变更，状态总是`fulfilled`，不会变成`rejected`。

返回的结果是个数组，每个成员是个对象，对应异步操作的结果。

```javascript
[
    {status: 'fulfilled', value: value},
    {status: 'rejected', reason: reason},
]
```

##### Promise.any()

ES2021 引入了[`Promise.any()`方法](https://github.com/tc39/proposal-promise-any)。该方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例返回。

只要参数实例有一个变成`fulfilled`状态，包装实例就会变成`fulfilled`状态；如果所有参数实例都变成`rejected`状态，包装实例就会变成`rejected`状态。

`Promise.any()`抛出的错误是一个 AggregateError 实例

**AggregateError**

ES2021 标准之中，为了配合新增的`Promise.any()`方法，引入一个新的错误对象`AggregateError`。

AggregateError 在一个错误对象里面，封装了多个错误。如果某个单一操作，同时引发了多个错误，需要同时抛出这些错误，那么就可以抛出一个 AggregateError 错误对象，把各种错误都放在这个对象里面。

AggregateError 本身是一个构造函数，用来生成 AggregateError 实例对象。

`AggregateError()`构造函数可以接受两个参数。

- errors：数组，它的每个成员都是一个错误对象。该参数是必须的。
- message：字符串，表示 AggregateError 抛出时的提示信息。该参数是可选的。

##### Promise.try()

实际开发中，经常遇到一种情况：不知道或者不想区分，函数`f`是同步函数还是异步操作，但是想用 Promise 来处理它。因为这样就可以不管`f`是否包含异步操作，都用`then`方法指定下一步流程，用`catch`方法处理`f`抛出的错误。

这可以使用async和new Promise实现，但过于繁琐，现在有一个[提案](https://github.com/ljharb/proposal-promise-try)，提供`Promise.try`方法替代这些写法。

使用Promise.try()后，可以统一用`promise.catch()`捕获所有同步和异步的错误。

#### 实例方法

##### then(onFulfilled, onRejected)

then方法为 Promise 实例添加状态改变时的回调函数，返回一个promise。

第一个参数是`resolved`状态的回调函数，第二个参数是`rejected`状态的回调函数，这两个参数可选，如果没传入或传入非函数类型，promise会在内部分别替换为 `(x) => x`、"Thrower" 函数。

then方法执行后默认返回一个新的成功状态的Promise对象，如果需要返回特定的Promise对象需要实例化一个Promise对象并返回。

在then的回调函数中,return的数据如果不是Promise，会默认用Promise包装。

```javascript
new Promise(resolve=>{})
.then(()=>{
    return "hello";
    /*equals to 
    return new Promise(resolve=>{resolve("hello")});
    */
})
```

> 当resolve 函数接收的是Promise对象时，后面的then 会根据传递的Promise 对象的状态变化决定执行哪一个回调

##### catch()

catch(rejection)用来指定发生错误时的回调函数，相当于then(null,rejection)，返回一个promise。

Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。

如果then中没有相应状态的回调函数，则此处的then不被执行，状态向下传递。

##### finally()

finally接收一个回调函数，用于指定不管 Promise 对象最后状态如何，都会执行的操作。

`finally`方法的回调函数不接受任何参数，`finally`方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果。

finally(handler)相当于then方法的如下表示：

```javascript
  .then{
      result=>{
          handler();
          return result;
      },
      err=>{
          handler();
          return new Promise((resolve,reject)=>{
              reject(err);
          })
      }
  }
```

### 执行时序

每个代理都是由事件循环驱动的，事件循环负责收集用事件（包括用户事件以及其他非用户事件等)、对任务进行排队以便在合适的时候执行回调。然后它执行所有处于等待中的JavaScript任务，然后是微任务，然后在开始下一次循环之前执行一些必要的渲染和绘制操作。

回调队列里等待执行的任务是宏任务，宏任务执行过程中会有一些额外的需求，这些需求称为微任务。微任务在当前宏任务的执行结束后执行。

使用微任务的最主要原因简单归纳为:

* 减少操作中用户可感知到的延迟
* 确保任务顺序的一致性，即便当结果或数据是同步可用的
* 批量操作的优化

Promise、MutationObserver、queueMicrotask、node端的process.nextTick都会作为微任务进入微任务队列。

script、setTimeout、setInterval、node的setImmediate都是宏任务。

### 源码模拟

```javascript
const PENDING = 0;
const FULFILLED = 1;
const REJECTED = 2;
function Promise(callBack) {
    this._state = PENDING;
    this._value = null;
    this._successHandlers = [];
    this._failHandlers = [];
    
    try{
        callBack(resolve, reject);
    }catch (err) {
        reject(err);
    }
    
    const self = this;
    function resolve(value) {
      if (value instanceof Promise) {
          if (value === self) throw new Error('Chaining cycle detedcted for promise')
          return value.then(resolve, reject);
      }
        setTimeout(()=> {
            if (self._state != PENDING) return;
            self._value = value;
            self._state = FULFILLED;
            self._successHandlers.forEach(handler => handler(value));
        })
    }
    
    function reject(reason) {
        setTimeout(()=> {
            if (self._state != PENDING) return;
            self._value = reason;
            self._state = REJECTED;
            self._failHandlers.forEach(handlers => handlers(reason));
        })
    }
}

const defaultSuccessHandler = value => value;
const defaultFailHandler = reason => {throw new Error(reason)};

Promise.prototype.then = function (successHandler, failHandler) {
    if (typeof successHandler != 'function') {
        successHandler = defaultSuccessHandler;
    };
    if (typeof failHandler != 'function') {
        failHandler = defaultFailHandler;
    }
    
    return new Promise((resolve, reject) => {
        const wrapFunc = wrapProccess(resolve, reject);
        const successCallBack = wrapFunc(successHandler);
        const failCallBack = wrapFunc(failHandler);
        
        switch (this._state) {
            case PENDING:
                this._successHandlers.push(successCallBack);
                this._failHandlers.push(failCallBack);
             	break;
            case FULFILLED:
               	successCallBack(this._value);
                break;
            case REJECTED:
                failCallBack(this._value);
                break;
        }
    })
}

Promise.prototype.catch = function (failHandler) {
    return this.then(,failHandler);
}

Promise.prototype.finally = function (handler) {
    return this.then(value => {
        return Promise.resolve(handler()).then(() => value);
    }, reason => {
        return Promise.resolve(handler()).then(() => Promise.reject(reason));
    })
}

Promise.reject = (value) => {
    return new Promise((,reject) => {
        reject(value);
    })
}

Promise.all = (promises) => {
    const wrappedPromises = promises.map(Promise.resolve);
    return new Promise((resolve, reject) => {
        let finishedPromise = 0;
        const results = [];
        wrappedPromises.forEach((promise, i) => promise.then((value) => {
            results[i] = value;
            finishedPromise++;
            if (finishedPromise === wrappedPromises.length) resolve(results);
        }, reject));
    })
}

Promise.race = promises => {
    const wrappedPromises = promises.map(Promise.resolve);
    return new Promise((resolve, reject) => {
        wrappedPromises.forEach(promise => promise.then(resolve, reject));
    })
}

Promise.resolve = (value) => {
    if (value instanceof Promise) return value;
    if (value.then){
        return new Promise((resolve, reject) => {
            value.then(resolve, reject);
        })
    }
    return new Promise((resolve) => {
        resolve(value);
    })
}

function wrapProccess(successHandler, errHandler) {
    return func => (...args) => {
        try {
            successHandler(func(...args));
        } catch (err) {
            errHandler(func)
        }
    }
}
```

## Generator异步方案

promise虽然解决了回调函数嵌套的问题，但是依旧使用回调函数，无法达到同步代码的可读性。

es2015提供了Generator语法，可以用于解决这个问题。

### 协程

协程（coroutine）是一种程序运行的方式，可以理解成“协作的线程”或“协作的函数”。

Generator 函数是 ES6 对协程的实现，但属于不完全实现。如果是完全执行的协程，任何函数都可以让暂停的协程继续执行。

#### 辨析

传统的“子例程”（subroutine）采用堆栈式“后进先出”的执行方式，只有当调用的子函数完全执行完毕，才会结束执行父函数。

协程与其不同，多个线程（单线程情况下，即多个函数）可以并行执行，但是只有一个协程（或函数）处于正在运行的状态，其他协程（或函数）都处于暂停态（suspended），协程（或函数）之间可以交换执行权。

普通线程可以有多个线程处于运行状态，但是运行的协程只能有一个，其他协程都处于暂停状态。线程是抢先式的，到底哪个线程优先得到资源，必须由运行环境决定，但是协程是合作式的，执行权由协程自己分配。

#### 运行流程

协程的运行流程大致如下。

- 第一步，协程`A`开始执行。
- 第二步，协程`A`执行到一半，进入暂停，执行权转移到协程`B`。
- 第三步，（一段时间后）协程`B`交还执行权。
- 第四步，协程`A`恢复执行。

### 异步流程管理

Generator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权（即暂停执行）。

整个 Generator 函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用`yield`语句注明。

Generator 函数可以暂停执行和恢复执行，这是它能封装异步任务的根本原因。除此之外，它还有两个特性，使它可以作为异步编程的完整解决方案：函数体内外的数据交换和错误处理机制。

虽然 Generator 函数将异步操作表示得很简洁，但是流程管理却不方便。

Generator 就是一个异步操作的容器。它的自动执行需要一种机制，当异步操作有了结果，能够自动交回执行权。

两种方法可以做到这一点。

（1）回调函数。将异步操作包装成 Thunk 函数，在回调函数里面交回执行权。

（2）Promise 对象。将异步操作包装成 Promise 对象，用`then`方法交回执行权。

#### Thunk函数

Thunk 函数是自动执行 Generator 函数的一种方法。

##### 参数求值策略

1. "传值调用"（call by value），即在进入函数体之前，就计算参数值，再将这个值传入函数
2. “传名调用”（call by name），即直接将表达式传入函数体，只在用到它的时候求值

编译器的“传名调用”实现，往往是将参数放到一个临时函数之中，再将这个临时函数传入函数体。这个临时函数就叫做 Thunk 函数。

##### JavaScript 的 Thunk 函数

JavaScript 语言是传值调用，它的 Thunk 函数含义有所不同。在 JavaScript 语言中，Thunk 函数替换的不是表达式，而是多参数函数，将其替换成一个只接受回调函数作为参数的单参数函数。

##### 流程管理

`yield`命令用于将程序的执行权移出 Generator 函数，那么就需要一种方法，将执行权再交还给 Generator 函数。

这种方法就是 Thunk 函数，因为它可以在回调函数里，将执行权交还给 Generator 函数。

```javascript
var fs = require('fs');
var thunkify = require('thunkify');
var readFileThunk = thunkify(fs.readFile);

var gen = function* (){
  var r1 = yield readFileThunk('/etc/fstab');
  console.log(r1.toString());
  var r2 = yield readFileThunk('/etc/shells');
  console.log(r2.toString());
};

function run(fn) {
  var gen = fn();

  function next(err, data) {
    if (err) throw err;
    var result = gen.next(data);
    if (result.done) return;
    result.value(next);
  }

  next();
}

run(gen);
```

#### co模块

```javascript
function run(gen, ...args) {
    const it = gen(...args);
    return handleNext();
    
    function handleNext(message) {
        const { value, done } = it.next(message);
        if (done) return Promise.resolve(value);
        return Promise.resolve(value).then(handleNext, handleErr);
    }
    
    function handleErr(err) {
        return Promise.resolve(it.throw(err)).then(handleNext);
    }
}
```

## Async / Await

ES2017 标准引入了 async 函数，是 Generator 函数的语法糖。

`async`函数就是将 Generator 函数的星号（`*`）替换成`async`，将`yield`替换成`await`。

`async`函数对 Generator 函数的改进，体现在以下四点。

1. 内置执行器

   Generator 函数的执行必须靠执行器，所以才有了`co`模块，而`async`函数自带执行器

2. 更好的语义

3. 更广的适用性

   `async`函数的`await`命令后面，可以是 Promise 对象和原始类型的值

4. 返回值是 Promise

async 函数的实现原理，就是将 Generator 函数和自动执行器，包装在一个函数里。

### 基本使用

`async`函数返回一个 Promise 对象，可以使用`then`方法添加回调函数。当函数执行的时候，一旦遇到`await`就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。

#### 使用形式

```javascript
// 函数声明
async function foo() {}

// 函数表达式
const foo = async function () {};

// 对象的方法
let obj = { async foo() {} };
obj.foo().then(...)

// Class 的方法
class Storage {
  constructor() {
    this.cachePromise = caches.open('avatars');
  }

  async getAvatar(name) {
    const cache = await this.cachePromise;
    return cache.match(`/avatars/${name}.jpg`);
  }
}

const storage = new Storage();
storage.getAvatar('jake').then(…);

// 箭头函数
const foo = async () => {};
```

#### 返回Promise对象

`async`函数返回一个 Promise 对象。`async`函数返回的 Promise 对象，必须等到内部所有`await`命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到`return`语句或者抛出错误。

`async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。

`async`函数内部抛出错误，会导致返回的 Promise 对象变为`reject`状态。抛出的错误对象会被`catch`方法回调函数接收到。

#### await命令

`await`命令后面是一个 Promise 对象，返回该对象的结果。如果不是 Promise 对象，就直接返回对应的值。

`await`命令后面是一个`thenable`对象（即定义了`then`方法的对象），那么`await`会将其等同于 Promise 对象。

`await`命令后面的 Promise 对象如果变为`reject`状态，则`reject`的参数会被`catch`方法的回调函数接收到，整个`async`函数都会中断执行。

* await命令只能用在async函数之中，如果用在普通函数，就会报错。
* 多个`await`命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。
* async 函数可以保留运行堆栈。

##### 顶层await

从 [ES2022](https://github.com/tc39/proposal-top-level-await) 开始，允许在模块的顶层独立使用`await`命令，主要目的是使用`await`解决模块异步加载的问题。

#### 错误处理

如果`await`后面的异步操作出错，那么等同于`async`函数返回的 Promise 对象被`reject`。防止出错的方法，是将其放在`try...catch`代码块之中。