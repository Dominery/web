# 函数

函数就是语句的封装，可以让这些代码方便地被复用。每个函数都是Function类型的实例，而且都与其他引用类型一样具有属性和方法。函数名是指向函数的指针，跟其余变量相同。

和变量类似，函数必须先定义然后才能使用

## 函数定义

函数定义的方式有三种：

1. 函数声明语法

2. 函数表达式

3. Function构造函数

   ```js
   let sum  = new Function('num1','num2','return num1+num2')
   ```

因函数是对象，函数名是变量指针，如果对函数名进行赋值操作，则该函数无法被访问。所以JavaScript中函数原生不支持方法重载。然而可以借助函数arguments或剩余参数实现函数重载。

### 函数声明提升

JavaScript中通过函数声明语法定义的函数可以先使用后声明，函数表达式的函数名应用变量提升规则。

如果变量与函数名相同，则函数优先提升，变量声明提升不能覆盖函数。

ES2017 [允许](https://github.com/jeffmo/es-trailing-function-commas)函数的最后一个参数有尾逗号（trailing comma）。

### 函数的参数

参数是函数内的一些待定值，在调用函数时,必须传入这些参数的具体值

函数的参数可多可少，多个参数之间需要用逗号隔开

圆括号内定义形式参数，调用时传入实际参数。

#### arguments

函数内arguments是一个类数组对象，存储接收到的实参。这个对象还有一个名叫callee的属性，该属性是一个指针，指向拥有这个arguments对象的函数。

arguments中的元素与参数列表参数对应，修改了arguments元素，参数值也会发生变化。

函数处于严格模式时，arguments元素值不会跟踪参数值。

当非严格模式中的函数参数列表中包含剩余参数、默认参数和解构赋值，那么arguments对象中的值不会跟踪参数的值。

#### 形实参个数不同

JavaScript中函数调用实参个数可以不需要与形参相同。函数形参个数可以通过函数的length属性获取。

实参个数少于形参个数，则剩余的形参值默认为undefined

#### 函数参数默认值

es6新增函数参数默认值设置。调用函数的时候如果形式参数传了实参，就用传递的参数；如果没传实参，就用默认值。

```javascript
const sum = (x=0,y=0)=>x+y;
```

* 生效条件

  不传参数，或者明确的传递undefined 作为参数，只有这两种情况下，默认值才会生效
  
* 使用参数默认值时，函数不能有同名参数

* 参数默认值是惰性求值

* 如果非尾部参数设置默认值，该参数位置必须传入undefined

* 设置默认值后，函数的length属性将返回没有指定默认值的参数个数

* 如果设置了默认值的参数不是尾参数，那么`length`属性也不再计入后面的参数了

函数参数的默认值，最好从参数列表的右边开始设置

#### 剩余参数

es6新增剩余参数，用于处理个数不确定的参数。

```javascript
const sum = (...args) => args.reduce((a,b)=>a+b);
```

剩余参数永远是个数组，即使没有值，也是空数组。

**注意事项**

* 箭头函数的参数部分即使只有一个剩余参数，也不能省略圆括号
* 使用剩余参数替代arguments获取实际参数
* 剩余参数只能是参数列表中最后一个参数
* 只要参数使用了默认值、解构赋值、或者扩展运算符，函数体内就不能显式指定严格模式。

**应用**

* 与解构赋值结合使用

  ```javascript
  const [num,...args] = [1,2,3,4,5];
  const {age,name,...args} = {name:"suyu",age:23,tel:110}
  ```

### 函数返回值

调用一个有返回值的函数，可以被当做一个普通值，从而可以出现在任何可以书写值的地方

调用函数时，一旦遇见return语句则会立即退出函数，将执行权交还给调用者

## 函数调用

### 函数对象

函数都包括length、prototype属性和apply、call、bind方法。

#### 属性

length属性保存函数命名时的参数，表示该函数预期传入（不包含默认参数和剩余参数）的参数个数。Function.length返回1。

name属性返回函数的名字，该属性值不可变。

* 如果将一个匿名函数赋值给一个变量，ES5 的`name`属性，会返回空字符串，而 ES6 的`name`属性会返回实际的函数名。
* `Function`构造函数返回的函数实例，`name`属性的值为`anonymous`。
* `bind`返回的函数，`name`属性值会加上`bound`前缀。

#### 方法

apply、call、bind能够用于设置函数调用时的this指向。

apply和call作用相同，设置函数this指向并调用函数，apply接收两个参数，作用域对象和数组。call接收多个对象，作用域对象和函数参数。

bind会返回一个设置函数this指向的新函数。

使用call、apply能够将对象与方法解耦。

**toString()**

[ES2019](https://github.com/tc39/Function-prototype-toString-revision) 对函数实例的`toString()`方法做出了修改。

`toString()`方法返回函数代码本身，以前会省略注释和空格。修改后的`toString()`方法，明确要求返回一模一样的原始代码。

### 尾调用

尾调用指某个函数的最后一步是调用另一个函数。

函数调用会在内存形成一个“调用记录”，又称“调用帧”（call frame），保存调用位置和内部变量等信息。

尾调用由于是函数的最后一步操作，所以不需要保留外层函数的调用帧，因为调用位置、内部变量等信息都不会再用到了，只要直接用内层函数的调用帧，取代外层函数的调用帧就可以了。这就叫做“尾调用优化”（Tail call optimization）。

* 只有不再用到外层函数的内部变量，内层函数的调用帧才会取代外层函数的调用帧
* 目前只有 Safari 浏览器支持尾调用优化，Chrome 和 Firefox 都不支持。

#### 尾递归

函数调用自身，称为递归。如果尾调用自身，就称为尾递归。

ES6明确规定，所有 ECMAScript 的实现，都必须部署“尾调用优化“。

ES6 的尾调用优化只在严格模式下开启，正常模式是无效的。

这是因为在正常模式下，函数内部有两个变量，可以跟踪函数的调用栈。

- `func.arguments`：返回调用时函数的参数。
- `func.caller`：返回调用当前函数的那个函数。

尾调用优化发生时，函数的调用栈会改写，因此上面两个变量就会失真。严格模式禁用这两个变量，所以尾调用模式仅在严格模式下生效。

#### 源码模拟

```javascript
function tco(f) {
  var value;
  var active = false;
  var accumulated = [];

  return function accumulator() {
    accumulated.push(arguments);
    if (!active) {
      active = true;
      while (accumulated.length) {
        value = f.apply(this, accumulated.shift());
      }
      active = false;
      return value;
    }
  };
}

var sum = tco(function(x, y) {
  if (y > 0) {
    return sum(x + 1, y - 1)
  }
  else {
    return x
  }
});
```

### 作用域链

先来认识函数的嵌套:一个函数内部也可以定义一个函数。和局部变量类似，定义在一个函数内部的函数是局部函数。

在函数嵌套中，变量会从内到外逐层寻找它的定义。

### 闭包

在JavaScript中，每次创建函数时都会创建闭包。闭包是函数本身和该函数声明时所处的环境状态的组合。即使函数不在其定义的环境中被调用，也能访问定义时所处环境的变量。

闭包允许将数据与操作该数据的函数关联起来。这与“面向对象编程”有少许相似之处。

闭包的用途

1. 记忆性
2. 模拟私有变量

闭包的注意点

不能滥用闭包，否则会造成网页的性能问题，严重时可能导致内存泄露。所谓内存泄漏是指程序中己动态分配的内存由于某种原因未释放或无法释放。

### IIFE

lIFE (lmmediately Invoked Function Expression，立即调用函数表达式)是一种特殊的JavaScript函数写法，一旦被定义，就立即被调用。

函数必须转为函数表达式才能被调用，可以通过括号、加号、减号等方式转换。

```javascript
(function(){
    statments;
})();
```

作用

1. 为变量赋值

2. 将全局变量变为局部变量

   ```javascript
   var arr = [];
   for(var i=0;i<5;i++){
       (function(i){
           arr.push(function(){
               console.log(i);
           });
       })(i);
   }
   ```


### 防抖与节流

防抖和节流都是防止在短时间内过于频繁的执行相同的任务。

函数防抖（debounce）是短时间内连续多次触发，但只执行最后一次，连续操作结束后再执行。 而节流（throttle）保证一段时间只执行一次。

以网页滚动为例，`debounce`要等到用户停止滚动后才执行，`throttle`则是如果用户一直在滚动网页，那么在滚动过程中还是会执行。

#### 防抖

函数防抖动的原理，主要是利用一次性定时器，延迟任务的执行，在延迟这段时间内， 如果任务再次被触发，则通过 clearTimeout 销毁上一次产生的定时器， 因为定时器的被销毁，之前被延迟执行的任务也会随之被取消执行。 

```javascript
function debounce(fn, delay = 500) {
    let timer = null;
    return function(...args) {
        if (!timer) {
            clearTimeout(timer);
        }
        timer = setTimeout(()=>fn(...args), delay);
    }
}
```

#### 节流

函数节流的原理，是采用时间戳，比较上次执行函数的时间和此时的时间，如果间隔过短，则不调用。

```javascript
function throttle(fn, delay = 500) {
    let time = null;
    return function(...args) {
        if (time == null || time + delay < Date.now()) {
            fn.apply(this, args);
            time = Date.now();
        }
    }
}
```

