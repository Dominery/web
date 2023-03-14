# ES6概览

es6全称为ECMAScript6，ECMAScript是语言标准，6是版本号。该标准在2015发行，又称ECMAScript2015，之后的版本迭代都以年份标识。

> ECMA是欧洲计算机制造商协会，是一个标准化组织，ECMA是由该组织指定的一个语言标准。

es6特指es2015，泛指es2015及以后的所有版本。

**历史版本**

历史上ECMAScript版本有1-3，5-6，其中4因为太过激进被废弃。

es3规定了do while、switch、正则表达式等操作。

es5引进了forEach、map、fliter、Object.crate等。

## 各版本特性

### ES2022

#### Top-level Await

ES2017引入async/await作为异步解决方案，await只能在async函数内使用，否则会报错。

顶层await允许在async函数外的模块作用域使用await关键字，允许模块充当大型异步函数。

使用场景：

1. 动态加载模块
2. 资源初始化
3. 依赖回退

#### Object.hasOwn()

以往可以使用`Object.prototype.hasOwnProperty()`判断一个属性是否属于一个对象。

Object.hasOwn()可以直接进行判断。

#### at()

at()是个数组/字符串方法，用于通过索引值获取数组元素，支持负值索引。

#### findLast(), findLastIndex()

[ES2022](https://github.com/tc39/proposal-array-find-from-last) 新增了两个方法`findLast()`和`findLastIndex()`，从数组的最后一个成员开始，依次向前检查，其他和`find()`保持不变。

#### error.cause()

new Error()可以传入第二个参数指定导致该错误的原因。

```javascript
try{
    
}catch(err) {
    throw new Error(
        "error",
        {cause: err}
	)
}

```

#### 正则表达式匹配索引

[ES2022](https://github.com/tc39/proposal-regexp-match-Indices) 新增了`d`修饰符，这个修饰符可以让`exec()`、`match()`的返回结果添加`indices`属性，在该属性上面可以拿到匹配的开始位置和结束位置。

* 如果正则表达式包含组匹配，那么`indices`属性对应的数组就会包含多个成员，提供每个组匹配的开始位置和结束位置。
* 如果正则表达式包含具名组匹配，`indices`属性数组还会有一个`groups`属性。该属性是一个对象，可以从该对象获取具名组匹配的开始位置和结束位置。
* 如果获取组匹配不成功，`indices`属性数组的对应成员则为`undefined`，`indices.groups`属性对象的对应成员也是`undefined`。

#### 类的实例成员

##### 公共实例成员

[ES2022](https://github.com/tc39/proposal-class-fields) 为类的实例属性，又规定了一种新写法。实例属性现在除了可以定义在`constructor()`方法里面的`this`上面，也可以定义在类内部的最顶层，不需要在实例属性前面加上`this`。

所有实例对象自身的属性都定义在类的头部，看上去比较整齐。

##### 私有字段、方法和访问器

[ES2022](https://github.com/tc39/proposal-class-fields)正式为`class`添加了私有属性和私有方法，方法是在属性名或方法之前使用`#`表示。

* 如果在类的外部使用，就会报错
* 不管在类的内部或外部，读取一个不存在的私有属性，也都会报错
* 私有属性也可以设置 getter 和 setter 方法
* 私有属性不限于从`this`引用，只要是在类的内部，实例也可以引用私有属性
* 私有属性和私有方法前面，也可以加上`static`关键字，表示这是一个静态的私有属性或私有方法

##### 静态公共字段

ES2022提供了类的静态属性，写法是在实例属性的前面，加上`static`关键字。

实例不能访问静态字段和方法，静态属性和方法可以被继承。

##### 类静态初始化块

ES2022 引入了[静态块](https://github.com/tc39/proposal-class-static-block)（static block），允许在类的内部设置一个代码块，在类生成时运行且只运行一次，主要作用是对静态属性进行初始化。

* 每个类允许有多个静态块，每个静态块中只能访问之前声明的静态属性
* 静态块的内部不能有`return`语句
* 静态块内部可以使用类名或`this`，指代当前类
* 静态块内可以访问类实例的私有字段和方法

### ES2021

#### String.replaceAll()

返回一个新的用`replacement`替换了`searchValue`的字符串。

* `searchValue`可以是字符串或者正则表达式，如果是正则表达式必须添加g修饰符，否则报错。

* `replacement`是一个字符串，表示替换的文本，其中可以使用一些特殊字符串。

  * `$&`：匹配的字符串。
  * `$` `：匹配结果前面的文本。
  * `$'`：匹配结果后面的文本。
  * `$n`：匹配成功的第`n`组内容，`n`是从1开始的自然数。这个参数生效的前提是，第一个参数必须是正则表达式。
  * `$$`：指代美元符号`$`

* `replacement`也可以是一个函数，该函数的返回值将替换掉第一个参数`searchValue`匹配的文本

  > 这个替换函数可以接受多个参数。第一个参数是捕捉到的匹配内容，第二个参数捕捉到是组匹配（有多少个组匹配，就有多少个对应的参数）。此外，最后还可以添加两个参数，倒数第二个参数是捕捉到的内容在整个字符串中的位置，最后一个参数是原字符串。

字符串的实例方法`replace()`只能替换第一个匹配，如果要替换所有的匹配，不得不使用正则表达式的`g`修饰符。

#### 数字分隔符

数字分隔符可以在数字之间创建可视化分隔符，通过`_`下划线分割数字，使数字更具可读性。

#### Promise.any()

ES2021 引入了[`Promise.any()`方法](https://github.com/tc39/proposal-promise-any)。该方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例返回。

只要参数实例有一个变成`fulfilled`状态，包装实例就会变成`fulfilled`状态；如果所有参数实例都变成`rejected`状态，包装实例就会变成`rejected`状态。

`Promise.any()`抛出的错误是一个 AggregateError 实例

#### 逻辑赋值操作符

新增`||=`、`&&=`、`??=`等逻辑操作符。

#### WeakRef

WeakSet 和 WeakMap 是基于弱引用的数据结构，[ES2021](https://github.com/tc39/proposal-weakrefs) 更进一步，提供了 WeakRef 对象，用于直接创建对象的弱引用。

#### FinalizationRegistry

[ES2021](https://github.com/tc39/proposal-weakrefs#finalizers) 引入了清理器注册表功能 FinalizationRegistry，用来指定目标对象被垃圾回收机制清除以后，所要执行的回调函数。

### ES2020

#### BigInt

ES2020新增了BigInt数据类型，可以表示任意大的整数，作为第八种基本类型。

#### 空值合并运算符

空值合并运算符，`??`，该运算符为二元运算符，当且仅当运算符左侧操作数类型为null或undifined，返回右侧操作数，否则返回左侧操作数。

#### 可选链操作符

可选链运算符,`?.`，能够降低对象多层属性安全访问的复杂度。

可选链运算符特性：

1. 短路机制，如果可选链运算符左侧操作数的求值结果为undefined或null，那么右侧的操作数不会再被求值
2. `?.`后不能为数字，否则作为三元运算符

#### Promise.allSettled()

`Promise.allSettled()`方法，用来确定一组异步操作是否都结束了（不管成功或失败）。

`Promise.allSettled()`方法接受一个数组作为参数，数组的每个成员都是一个 Promise 对象，并返回一个新的 Promise 对象。只有等到参数数组的所有 Promise 对象都发生状态变更（不管是`fulfilled`还是`rejected`），返回的 Promise 对象才会发生状态变更，状态总是`fulfilled`，不会变成`rejected`。

返回的结果是个数组，每个成员是个对象，对应异步操作的结果。

#### String.matchAll()

matchAll()返回一个包含所有匹配正则表达式的结果及分组捕获组的迭代器。

## 解决语法问题

### 变量声明

es6通过关键字let、const替代var来声明变量和常量。

> var、let声明的就是变量，变量一旦初始化之后，还可以重新赋值。
>
> const声明的是常量，常量一旦初始化，就不能重新赋值了，否则就会报错

**注意事项**

1. 使用const声明常量时必须初始化

2. const声明的常量，允许在不重新赋值的情况下修改它的值

   > 基本数据类型无法不重新赋值进行修改
   >
   > 引用数据类型可以对其属性进行修改

**let、const与var的区别**

* 重复声明

  var允许对已经存在变量重新声明，let和const如果重复声明会报错

* 变量提升

  var会提升变量的声明到当前作用域的顶部，let和const不存在变量提升

* 暂时性死区

  代码块内使用let、const的变量或常量绑定在该代码块，不受外部作用域的影响。即在代码块内，使用let命令声明变量之前，该变量都是不可用的。

  > ES6规定暂时性死区和let、const语句不出现变量提升，主要是为了规范代码书写，减少运行时错误，防止总之，在变量声明前就使用这个变量，从而导致意料之外的行为。

* window对象的属性和方法

  全局作用域中，var 声明的变量，通过function声明的函数，会自动变成window对象的属性或方法。let和const不会。

### 块级作用域

es5只有全局作用域和函数作用域，es2015新增了块级作用域。

var声明的变量没有块级作用域。let、const声明的变量有块级作用域。

块级作用域：花括号、循环、条件控制。

变量使用时会通过作用域链查找。

## 增强语法

### 字符串

#### Unicode表示法

ES6 加强了对 Unicode 的支持，允许采用`\uxxxx`形式表示一个字符，其中`xxxx`表示字符的 Unicode 码点。

#### 遍历器接口

ES6 为字符串添加了遍历器接口，使得字符串可以被`for...of`循环遍历。

这个遍历器最大的优点是可以识别大于`0xFFFF`的码点。

#### 模板字符串

通过反引号``标记的字符串为模板字符串。

模板字符串可以通过${}注入数据，减少了字符串拼接的复杂工作。

* 模板字符串中，所有的空格换行或缩进都会被保留在输出之中
* 输出`或\等特殊字符需要使用\转义
* 只要最终可以得出一个值的就可以注入到模板字符串中

**标签函数**

在模板字符串前添加一个函数名，模板字符串会由`${}`表达式分割成数组，作为第一个参数传给该函数，数组有个raw属性，存储原本未转义的字符串，表达式的值也会依次作为参数传给该函数。函数的返回值会作为模板字符串拼接后的结果。

```javascript
function tag(strs, name) {
    return [name, 'said', ...strs].join(' ');
}
const name = 'tom';
console.log(tag`hello ${name}`)
```

应用场景：

* 检查模板字符串是否存在不安全字符
* 语言替换

### 数值

#### 二进制和八进制表示法

ES6 提供了二进制和八进制数值的新的写法，分别用前缀`0b`（或`0B`）和`0o`（或`0O`）表示。

#### 数字分隔符

[ES2021](https://github.com/tc39/proposal-numeric-separator)，允许 JavaScript 的数值使用下划线（`_`）作为分隔符。

#### BigInt数据类型

[ES2020](https://github.com/tc39/proposal-bigint) 引入了一种新的数据类型 BigInt（大整数）。

### Symbol

ES5 的对象属性名都是字符串，这容易造成属性名的冲突。ES6 引入`Symbol`，保证每个属性的名字都是独一无二的。

#### 内置的Symbol

ES6 提供了 11 个内置的 Symbol 值，指向语言内部使用的方法。

#### Symbol.for(), Symbol()

Symbol值可以通过Symbol()、Symbol.for()函数进行创建，通过Symbol()创建的值是唯一的。

### 函数

#### 参数默认值

es6新增函数参数默认值设置。调用函数的时候如果形式参数传了实参，就用传递的参数;如果没传实参，就用默认值。

```javascript
const sum = (x=0,y=0)=>x+y;
```

* 生效条件

  不传参数，或者明确的传递undefined 作为参数，只有这两种情况下，默认值才会生效

函数参数的默认值，最好从参数列表的右边开始设置

#### 函数参数逗号

es2017允许在函数最后一个参数后添加逗号。

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

#### 箭头函数

通过=>定义的匿名函数是箭头函数。

箭头函数的结构是(参数)=>{函数体}。可以将箭头函数赋值给一个变量，从而能够多次调用该函数。

**注意事项**

在某些情况下箭头函数可以进一步化简。

* 单个参数

  单个参数可以省略括号

* 单行函数体

  单行函数体可以省略花括号和return

* 单行对象

  返回单行对象需要在花括号外加上圆括号以区分函数体的花括号。

**this指向**

1. 全局作用域中的this指向window
2. 一般函数中的this指向需要在调用时才确定，this指向和函数调用位置无关，指向调用函数的对象
3. 没有具体调用对象，this指向undefined，在非严格模式下转向window
4. 箭头函数没有本身的this，箭头函数中的this指向该函数定义时所在作用域指向的对象，而不是使用时所在的作用域指向的对象。

**与普通函数区别**

1. 没有自身的arguments
2. this指向父级作用域
3. 不可以使用yield
4. 没有prototype属性，不能用作构造函数

#### catch参数省略

[ES2019](https://github.com/tc39/proposal-optional-catch-binding) 做出了改变，允许`catch`语句省略参数。

### 对象

#### 属性的简洁表示法

ES6 允许在大括号里面，直接写入变量和函数，作为对象的属性和方法。

#### 属性名表达式

ES6 允许字面量定义对象时，把表达式放在方括号内。

#### super关键字

ES6 新增了关键字`super`，指向当前对象的原型对象。

#### 对象扩展运算符

ES2018 将这个运算符[引入](https://github.com/sebmarkbage/ecmascript-rest-spread)了对象。

#### AggregateError错误对象

ES2021 标准之中，为了配合新增的`Promise.any()`方法，引入一个新的错误对象`AggregateError`。

### 解构赋值

#### 数组的解构赋值

> 解析某—数据的结构，将需要的数据提取出来，赋值给变量或常量的操作是解构赋值。
>
> 只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。
>
> 解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于`undefined`和`null`无法转为对象，所以对它们进行解构赋值，都会报错。

**原理**

1. 模式(结构)匹配
2. 索引值相同的完成赋值

**默认值**

* 默认值的基本用法

  ```javascript
  const [a=0,b=1] = [];
  ```

* 只有数组成员严格等于undefined，对应的默认值才会生效

* 如果默认值是表达式，默认值表达式是惰性求值的。

* 默认值可以引用解构赋值的其他已经声明变量

**应用**

1. 常见类数组解构赋值，arguments，NodeList(返回的dom对象数组)

2. 函数参数的解构赋值

   ```javascript
   const add = ([x=0,y=0])=>x+y;
   ```

3. 交换变量值

#### 对象的解构赋值

**原理**

1. 模式匹配

2. 属性名相同的值完成赋值

   ```javascript
   const {age,username} = {username:"suyu",age:23};
   //完整的写法
   const {'age':age,'username':username} = {username:"suyu",age:23};
   //取别名
   const {age:age,username:uname} = {username:"suyu",age:23};
   ```

**注意事项**

* 默认值通过等号设置，只有对象属性值严格等于undefined时，才会生效
* 如果将一个已经声明的变量用于解构赋值，整个赋值过程需要在圆括号内进行
* 可以获取到继承的属性
* 如果解构嵌套对象，子对象所在的父属性不存在，将会报错
* 数组本质是对象，可以对数组使用对象解构赋值

**应用**

1. 函数参数的解构赋值
2. 嵌套对象

#### 其他数据类型的解构赋值

**字符串**

字符串既可以按数组形式来解构赋值，也可以按对象形式来解构赋值

* 数组形式的解构赋值

  ```javascript
  const [a,b] = "suyu";
  ```

* 对象形式解构赋值

  ```javascript
  const {0:a,1:b,length} = "suyu";
  ```

**数值和布尔值**

只能使用对象形式来解构赋值，运行时会将等号右边的值转换为对象

**undefined和null**

由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错

### for ... of 循环

es2015新增for ... of循环可以用来遍历迭代器对象。

通过for... of遍历字符串可以识别大于0xFFFF的码点。

### 运算符

#### 指数运算符

es2016提供了指数运算符`**`。

多个指数运算符连用时，是从最右边开始计算的。

#### 可选链运算符

 [ES2020](https://github.com/tc39/proposal-optional-chaining) 引入了“可选链运算符”（optional chaining operator）`?.`，能够降低对象多层属性安全访问的复杂度。

可选链运算符`?.`有三种写法。

- `obj?.prop` // 对象属性是否存在
- `obj?.[expr]` // 同上
- `func?.(...args)` // 函数或对象方法是否存在

```javascript
a?.b
// 等同于
a == null ? undefined : a.b

a?.[x]
// 等同于
a == null ? undefined : a[x]

a?.b()
// 等同于
a == null ? undefined : a.b()

a?.()
// 等同于
a == null ? undefined : a()
```

* `?.`运算符相当于一种短路机制，只要不满足条件，就不再往下执行。
* 使用`?.`运算符的场合，不应该使用圆括号。
* `?.`后不能为数字，否则会按照三元运算符进行处理

下面使用场景会报错

```javascript
// 构造函数
new a?.()
new a?.b()

// 链判断运算符的右侧有模板字符串
a?.`{b}`
a?.b`{c}`

// 链判断运算符的左侧是 super
super?.()
super?.foo

// 链运算符用于赋值运算符左侧
a?.b = c
```

#### 空值合并运算符

读取对象属性的时候，如果某个属性的值是`null`或`undefined`，有时候需要为它们指定默认值。常见做法是通过`||`运算符指定默认值。但是属性的值如果为空字符串或`false`或`0`，默认值也会生效。

[ES2020](https://github.com/tc39/proposal-nullish-coalescing) 引入了空值合并运算符，`??`，该运算符为二元运算符，当且仅当运算符左侧操作数类型为null或undifined，返回右侧操作数，否则返回左侧操作数。

* `??`本质上是逻辑运算，如果多个逻辑运算符一起使用，必须用括号表明优先级，否则会报错。
* 空值合并运算符目的就是跟链判断运算符`?.`配合使用

#### 逻辑赋值运算符

ES2021 引入了三个新的[逻辑赋值运算符](https://github.com/tc39/proposal-logical-assignment)`||=`、`&&=`、`??=`，将逻辑运算符与赋值运算符进行结合。它们可以为变量或属性设置默认值。

#### 展开运算符

**展开数组**

展开运算符可以将数组内元素迭代取出。

**构造字面量对象**

对象不能直接使用展开运算符，只有在构造字面量对象即周围有花括号时，可以将原有对象的属性复制。React传props时展开运算符不是复制的作用，React结合babel实现展开对象的功能。

通过展开运算符可以将对象属性存放到空对象中，构成新对象。

可以通过对象展开合并多个对象的属性。es6新增Object.assign()方法用来合并对象，但是不构成新对象，将其余对象属性添加到第一个对象中。

* 如果展开空对象，没有任何效果
* 如果展开的不是对象，则会自动将其转为对象，再将其属性罗列出来
* 如果展开运算符后面是字符串，它会自动转成一个类似数组的对象，因此返回的不是空对象

应用

* 克隆对象
* 用户参数和默认参数

### catch命令参数省略

JavaScript 语言的`try...catch`结构，以前明确要求`catch`命令后面必须跟参数，接受`try`代码块抛出的错误对象。

[ES2019](https://github.com/tc39/proposal-optional-catch-binding) 做出了改变，允许`catch`语句省略参数。

### class

#### 私有属性和方法

[ES2022](https://github.com/tc39/proposal-class-fields)正式为`class`添加了私有属性和私有方法，方法是在属性名或方法之前使用`#`表示。

#### 公共实例字段

[ES2022](https://github.com/tc39/proposal-class-fields) 为类的实例属性，又规定了一种新写法。实例属性现在除了可以定义在`constructor()`方法里面的`this`上面，也可以定义在类内部的最顶层，不需要在实例属性前面加上`this`。

#### 静态属性、静态方法、静态块

ES 2022 提供了在 JavaScript 中使用 `static` 关键字声明静态类字段的方法。

### Generator

为了更好解决异步回调函数问题，提供更好的异步解决方案，es2015实现了Generator语法。

### ES Modules

ES Modues是语言层面的模块化标准

## 新功能

### Error对象的cause属性

[ES2022](https://github.com/tc39/proposal-error-cause) 为 Error 对象添加了一个`cause`属性，可以在生成错误时，添加报错原因的描述。

```javascript
throw new Error('message', {cause: 'reason'})
```

### globalThis对象

JavaScript 语言存在一个顶层对象，它提供全局环境（即全局作用域），所有代码都是在这个环境中运行。但是，顶层对象在各种实现里面是不统一的。

- 浏览器里面，顶层对象是`window`，但 Node 和 Web Worker 没有`window`。
- 浏览器和 Web Worker 里面，`self`也指向顶层对象，但是 Node 没有`self`。
- Node 里面，顶层对象是`global`，但其他环境都不支持。

[ES2020](https://github.com/tc39/proposal-global) 在语言标准的层面，引入`globalThis`作为顶层对象。也就是说，任何环境下，`globalThis`都是存在的，都可以从它拿到顶层对象，指向全局环境下的`this`。

### 数值拓展方法

| 方法                      | 说明                  |
| ------------------------- | --------------------- |
| Number.isFinite(val)      | 判断val是否溢出       |
| Number.isNaN(val)         | 判断val是否NaN        |
| Number.parseInt(str)      | 解析str成整数         |
| Number.parseFloat(str)    | 解析str成数字         |
| Number.isInteger(val)     | 判断val是否整数       |
| Number.isSafeInteger(val) | 判断val是否没溢出整数 |



### 数组拓展方法

通过调用数组的keys()方法可以得到索引的可迭代对象，对其进行迭代。

es6提供了value()方法用来获取值的可迭代对象。

entries()方法可以得到由索引和值组成的数组的可迭代对象。

```javascript
for(const key of arr.keys()){
    
}
for(const [key,value] of arr.entries()){
    
}
```

es2016提供了includes()方法用于判断数组是否存在某个指定元素，indexOf()无法查找NaN。

### 字符串拓展方法

| 方法                   | 说明                                   | 版本   |
| ---------------------- | -------------------------------------- | ------ |
| includes(str)          | 判断字符串中是否有str                  | es2015 |
| startsWith(str)        | 判断字符串是否以str开头                | es2015 |
| endsWith(str)          | 判断字符串是否以str结尾                | es2015 |
| padStart(num, str)     | 用给定str填充字符串开始位置到num长度   | es2017 |
| padEnd(num, str)       | 用给定str填充字符串结束位置到num长度   | es2017 |
| String.fromCodePoint() | 识别大于0xFFFF的字符                   | es6    |
| String.raw()           | 作为模板函数，返回斜杠都被转义的字符串 | es6    |

### 对象拓展方法

| 方法                               | 说明                                                         | 版本   |
| ---------------------------------- | ------------------------------------------------------------ | ------ |
| Object.assign(target, obj1,obj2)   | 参数要求是对象，将第二个及之后对象的属性依次覆盖到target中，返回target | es2015 |
| Object.is(val1, val2)              | ===运算符无法比较+0、-0,NaN、NaN，Object.is()可以            | es2015 |
| Object.values(obj)                 | 获取对象的属性值                                             | es2017 |
| Object.entries(obj)                | 以数组返回对象的属性和属性值                                 | es2017 |
| Object.getOwnPropertyDescriptors() | 返回指定对象的属性描述符                                     | es2017 |
| Object.fromEntries(entries)        | 用于将一个键值对数组转为对象。                               |        |
| Object.hasOwn(obj, attr)           | 判断attr是否为obj自身属性                                    | es2022 |

### Proxy

Proxy对象构造器接收两个参数target、handler

target

要使用 `Proxy` 包装的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）。

handler

一个通常以函数作为属性的对象，各属性中的函数分别定义了在执行各种操作时代理 `p` 的行为。

```javascript
const person = {
    name: 'tom',
    age: 23,
}

const personProxy = new Proxy(person, {
    get(target, property) {
        console.log(property);
        return property in target ? target[property] : null
    } 
})
```

**handler监视操作**

[`handler.getPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/getPrototypeOf)

[`Object.getPrototypeOf`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/GetPrototypeOf) 方法的捕捉器。

[`handler.setPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/setPrototypeOf)

[`Object.setPrototypeOf`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf) 方法的捕捉器。

[`handler.isExtensible()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/isExtensible)

[`Object.isExtensible`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/isExtensible) 方法的捕捉器。

[`handler.preventExtensions()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/preventExtensions)

[`Object.preventExtensions`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/preventExtensions) 方法的捕捉器。

[`handler.getOwnPropertyDescriptor()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/getOwnPropertyDescriptor)

[`Object.getOwnPropertyDescriptor`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor) 方法的捕捉器。

[`handler.defineProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/defineProperty)

[`Object.defineProperty`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 方法的捕捉器。

[`handler.has()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/has)

[`in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/in) 操作符的捕捉器。

[`handler.get()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/get)

属性读取操作的捕捉器。

[`handler.set()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/set)

属性设置操作的捕捉器。

[`handler.deleteProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/deleteProperty)

[`delete`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/delete) 操作符的捕捉器。

[`handler.ownKeys()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/ownKeys)

[`Object.getOwnPropertyNames`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames) 方法和 [`Object.getOwnPropertySymbols`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols) 方法的捕捉器。

[`handler.apply()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/apply)

函数调用操作的捕捉器。

[`handler.construct()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/construct)

[`new`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new) 操作符的捕捉器。

### Reflect

Reflect提供了一系列静态方法，一套用于操作对象的api，这些方法是Proxy处理对象的默认实现。

Refect对象的方法与Proxy的handler方法基本一致。

### 异步方案

#### Promise

Promise提供了全新的异步编程方案，用于解决回调函数嵌套问题。

#### 迭代器

迭代器（iterator）是按需创建的一次性对象。每个迭代器都会关联一个可迭代对象，而迭代器会暴露迭代其关联可迭代对象的API。迭代器无须了解与其关联的可迭代对象的结构，只需要知道如何取得连续的值。

**iterator的使用**

数组可以使用方括号语法访问Symbol.iterator方法，通过调用该方法，将获得一个iterator可迭代对象，iterator可以用next方法获取一个IteratorResult对象。该对象有两个属性表示迭代的下一个元素和迭代是否结束。

```javascript
const it = [1,2][Symnbol.iterator]();
it.next();//{value:1,done:false}
it.next();//{value:2,done:false}
it.next();//{value:undefied,done:true}

let next;
const ite = [1,2][Symbol.iterator]();
while(!(next=ite.next()).done){
    console.log(next.value);
}
```

> Symbol是ES6中引入的一种新的基本数据类型，用于表示一个独一无二的值。它是JavaScript 中的第七种数据类型。

为了方便iterator使用，es6提供for...of语法糖。

**可迭代**

只要对象有Symbol.iterator方法，并且这个方法可以生成可迭代对象，该对象就是可迭代的。从而可以通过for...of循环来统一迭代过程。

* 原生可迭代对象

  数组、字符串、Set、Map、arguments、NodeList

* 非原生可迭代

  普通的对象

#### Async/Await

es2017提出了async/await用于异步编程的解决方案。

es2022提出了Top-Level await，await可以在async函数外使用。

## 新数据类型及结构

### Set

Set是一系列无序、没有重复值的数据集合。Set可以用于数组或字符串去重。

### Map

map和对象都是键值对集合。但对象使用字符串作为键，map的键可以是基本数据类型或引用数据类型。

### 弱引用

#### WeakSet

WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别。

#### WeakMap

`WeakMap`结构与`Map`结构类似，也是用于生成键值对的集合。

#### WeakRef

WeakSet 和 WeakMap 是基于弱引用的数据结构，[ES2021](https://github.com/tc39/proposal-weakrefs) 更进一步，提供了 WeakRef 对象，用于直接创建对象的弱引用。

#### FinalizationRegistry

[ES2021](https://github.com/tc39/proposal-weakrefs#finalizers) 引入了清理器注册表功能 FinalizationRegistry，用来指定目标对象被垃圾回收机制清除以后，所要执行的回调函数。

### Symbol

Symbol值可以通过Symbol()、Symbol.for()函数进行创建，通过Symbol()创建的值是唯一的，Symbol.for()会在全局作用域里查找有该标识的Symbol，如果不存在会创建一个Symbol。

Symbol的使用场景

1. 作为内置属性名称，避免同属性名覆盖
2. 使用Symbol代替常量
3. 作为私有变量

### 兼容性

主流浏览器的最新版本几乎全部支持ES6

IE老版本等不支持的浏览器，可以用 Babel转码