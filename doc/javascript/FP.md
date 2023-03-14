# 函数式编程

函数式编程(Functional Programming, FP)，FP是编程范式之一。

> 编程范式有面向过程编程、面向对象编程、函数式编程
>
> * 面向对象编程：把现实世界中的事物抽象成程序世界中的类和对象，通过封装、继承和多态来演示事物事件的联系


函数式编程：把现实世界的事物和事物之间的联系抽象到程序世界(对运算过程进行抽象)

函数式编程中程序的本质是一种映射关系，输入通过某种运算获得相应的输出，这个映射关系称为函数。

## 基础

### 函数是一等公民

函数是一等公民主要有以下特性：

* 函数可以存储在变量中

* 函数作为参数

* 函数作为返回值


在JavaScript中函数就是一个普通的对象，可以存储到变量/数组中，还可以作为另一个函数的参数和返回值，甚至可以在程序运行的时候通过new Function("alert(1)')来构造一个新的函数。

### 高阶函数

高阶函数主要具备以下特征之一：

* 参数是函数
* 返回值是函数

高阶函数用来抽象通用的问题。

### 闭包

在JavaScript中，每次创建函数时都会创建闭包。闭包是函数本身和该函数声明时所处的环境状态的组合。即使函数不在其定义的环境中被调用，也能访问定义时所处环境的变量。

闭包变量会存放在堆中，不会从执行栈释放。

### 纯函数

纯函数：相同的输入始终要得到相同的输出。

纯函数具有以下优势：

* 利于测试
* 利于并行处理

## 函数式技术

### 柯里化

柯里化能够将多参数函数转变为单参数函数。

```javascript
function curry(fn, ...args) {
    return fn.length <= args.length ? fn(...args) : (...arg) => curry.bind(null, fn, ...arg);
}
```

柯里化可以让我们给一个函数传递较少的参数得到一个已经记住了某些固定参数的新函数，这是一种对函数参数的缓存。

柯里化优势

* 让函数变的更灵活,让函数的粒度更小
* 可以把多元函数转换成一元函数
* 可以组合使用函数产生强大的功能。

### 函戴组合

函数组合：如果一个函数要经过多个函数处理才能得到最终值，这个时候可以把中间过程的函数合并成一个函数

* 函数就像是数据的管道，函数组合就是把这些管道连接起来，让数据穿过多个管道形成最终结果
* 函数组合默认是从右到左执行

redux中的compose实现

```javascript
function compose(...funcs) {
    if (funcs.length == 1) return funcs[0];
    return funcs.reduce((func1, func2) => (...args) => func1(func2(...args)));
}
```

koa2中compose实现

```javascript
function compose(middlewares) {
    
    return (context) => {
        return dispatch(0);
        let lastIndex = -1;
        
        function dispatch(i) {
            if (i <= lastIndex++) return Promise.reject(new Error('next() called multiple times'));
            if (i >= middlewares.length) return Promise.resolve();
            const middleware = middlewares[i];

            return Promise.resolve(middleware(context, dispatch.bind(null, i + 1)));
        }
    }
}
```

### Functor (函子)

函子在函数式编程中把副作用控制在可控的范围内、异常处理、异步操作等。

函子是一个特殊的容器，通过一个普通的对象来实现，该对象具有map方法，map方法可以运行一个函数对值进行处理(变形关系)

> 容器:包含值和值的变形关系(这个变形关系就是函数) 

```javascript
function functor(value) {
    return {
        map
    };
    function map(fn) {
        return functor(fn(value));
    }
}
```

* 函数式编程的运算不直接操作值，而是由函子完成
* 函子就是一个实现了map契约的对象
* 我们可以把函子想象成一个盒子，这个盒子里封装了一个值，想要处理盒子中的值，我们需要给盒子的map方法传递一个处理值的函数（纯函数)，由这个函数来对值进行处理
* 最终map方法返回一个包含新值的盒子(函子)

#### MayBe函子

我们在编程的过程中可能会遇到很多错误，需要对这些错误做相应的处理

MayBe 函子的作用就是可以对外部的空值情况做处理(控制副作用在允许的范围)

```javascript
function MayBe(value) {
    return {
        map
    };
    function map(fn) {
        return isNull(value) ? MayBe(value) : MayBe(fn(value));
    }
    
    function isNull(value) {
        return value === null;
    }
}
```

#### Either函子

Either两者中的任何一个，类似于if...else...的处理

异常会让函数变的不纯，Either函子可以用来做异常处理



#### IO函子

IO函子中的value是一个函数，这里是把函数作为值来处理

lO函子可以把不纯的动作存储到value中，延迟执行这个不纯的操作(惰性执行)，包装当前的操作

把不纯的操作交给调用者来处理