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

### 函数的参数

参数是函数内的一些待定值，在调用函数时,必须传入这些参数的具体值

函数的参数可多可少，多个参数之间需要用逗号隔开

圆括号内定义形式参数，调用时传入实际参数。

#### 形实参个数不同

JavaScript中函数调用实参个数可以不需要与形参相同。函数形参个数可以通过函数的length属性获取。

实参个数少于形参个数，则剩余的形参值默认为undefined

函数内arguments是一个类数组对象，存储接收到的实参。这个对象还有一个名叫callee的属性，该属性是一个指针，指向拥有这个arguments对象的函数。

#### 函数参数默认值

es6新增函数参数默认值设置。调用函数的时候如果形式参数传了实参，就用传递的参数;如果没传实参，就用默认值。

```javascript
const sum = (x=0,y=0)=>x+y;
```

* 生效条件

  不传参数，或者明确的传递undefined 作为参数，只有这两种情况下，默认值才会生效

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

**应用**

* 与解构赋值结合使用

  ```javascript
  const [num,...args] = [1,2,3,4,5];
  const {age,name,...args} = {name:"suyu",age:23,tel:110}
  ```


#### 函数重载实现

```js
function addFunc(oldFn,newFn) {
    return function (...args) {
        if(args.length === newFn.length){
            return newFn.apply(this,args)
        }
        if(typeof oldFn === "function"){
            return oldFn.apply(this,args)
        }
    }   
}
function sum() {
    return 1
}

sum = addFunc(sum,function (num1,num2) {
    return num1 + num2
})
```

### 函数返回值

调用一个有返回值的函数，可以被当做一个普通值，从而可以出现在任何可以书写值的地方

调用函数时，一旦遇见return语句则会立即退出函数，将执行权交还给调用者

## 函数调用

### 函数属性和方法

函数都包括length、prototype属性和apply、call、bind方法。

length属性保存函数命名时的参数。

apply、call、bind能够用于设置函数调用时的this指向。

apply和call作用相同，设置函数this指向并调用函数，apply接收两个参数，作用域对象和数组。call接收多个对象，作用域对象和函数参数。

bind会返回一个设置函数this指向的新函数。

使用call、apply能够将对象与方法解耦。

### 作用域链

先来认识函数的嵌套:一个函数内部也可以定义一个函数。和局部变量类似，定义在一个函数内部的函数是局部函数。

在函数嵌套中，变量会从内到外逐层寻找它的定义。

### 闭包

在JavaScript中，每次创建函数时都会创建闭包。闭包是函数本身和该函数声明时所处的环境状态的组合。即使函数不在其定义的环境中被调用，也能访问定义时所处环境的变量。

闭包它允许将数据与操作该数据的函数关联起来。这与“面向对象编程”有少许相似之处。

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

   