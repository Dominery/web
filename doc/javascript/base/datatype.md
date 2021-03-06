# 数据类型

计算机程序是通过操作值来运行的，数据类型是值的一个属性，它定义了值的行为，描述在该值上允许执行的操作，以使其区别于其他值。

在ECMAScript 2015规范中定义了七种内置数据类型：number、string、boolean、undifined、null、symbol、object。除object外，其余类型都属于基本数据类型。

Object.prototype.toString()可以检测所有数据类型。

typeof运算符可以检测值类型，返回类型的字符串值。然而存在两个例外，null是唯一一个用typeof检测会返回object的基础数据类型，函数是对象，用typeof检测会返回function。

| 类型名    | typeof检测结果 |
| --------- | -------------- |
| number    | number         |
| string    | string         |
| boolean   | boolean        |
| undefined | undefined      |
| null      | object         |
| object    | object         |
| symbol    | symbol         |

> 基本数据类型存储在栈中，变量赋值时采用深拷贝，引用数据类型存储在堆里，变量赋值时复制了引用指针，引用指针指向堆里的对象的地址。

**字面量**

在计算机科学中，字面量用于在源代码中表示某个固定值。在JavaScript程序中，字面量不是变量，它是直接给出的固定值。

## 基本数据类型

### 数字类型

JavaScript使用双精度64位浮点数格式（IEEE 754）来表示数字，因此所有数字本质上都是浮点数。IEE 754规定1位符号位、11位指数位、52位尾数位，js中整数使用尾数位进行，因此最大为2^53^-1(尾数有一个隐藏位)。

#### 数字的表示

表达无整数位的小数时，可以省略整数位。

js中如果一个数字大于等于1e21输出时会使用科学计数法（如`1e+21`），否则转换为十进制输出。

不同进制的数字

二进制数值以0b开头，八进制以0o开头，十六进制以0x开头

#### 数字方法

| 方法               | 功能                                        |
| ------------------ | ------------------------------------------- |
| toFixed(a)         | 返回a位小数位的字符串表示                   |
| toExponential()    | 返回以指数表示法表示的数值字符串            |
| toPrecision(a)     | 根据a位数决定调用toFixed()或toExponential() |
| Number.isInteger() | 检测一个值是否为整数                        |

数字方法可以作用于数字变量和字面量，但数字字面量中`.`是数字字符，会优先被识别为数字字面量的一部分，之后才是数字属性访问运算符。

```js
10.toFixed(2) // SyntaxError
10..toFixed(2)
(10).toFixed(2)
10 .toFixed(2)
```

* NaN

  NaN是英语“not a number”的意思，即“不是一个数”，却是一个数字类型的值

  0除以0的结果是NaN，事实上，在数学运算中，若结果不能得到数字，其结果往往都是NaN。

  isNaN()函数可以用来判断变量值是否为NaN。它的机理是:只要该变量传入Number()的执行结果是NaN，则isNaN()函数都会得到true。

  es6提供了Number.isNaN()函数，会先判断变量是否是数字类型，再判断是否为NaN。

* infinite

  非零数字除以0时，结果是Infinity或-Infinity

  如果某次计算的结果得到了一个超出JavaScript数值范围的值，那么这个数值将被自动转换成特殊的Infinity值。
  
  要想确定一个数值是不是有穷的，可以使用isFinite()函数。
  
  > Number.NEGATIVE_INFINITY和Number.POSITIVE_INFINITY可以得到负和正Infinity的值

### 字符串类型

JavaScript使用UTF-16编码来表示一个字符。UTF-16编码以两个字节作为一个编码单元，每个字符使用一个编码单元或者两个编码单元来表示。

JSON.stringify()和JSON.parse()可以将js对象序列化和反序列化，如果一个对象中存在循环引用、undifined、symbol、function等不符合JSON结构的值，那么JSON.stringify()方法不能正常执行，循环引用会报错，其余情况会被忽略，在数组中返回null。如果对象中定义了toJSON()方法，JSON字符串化时会首先调用该方法，然后用它的返回值来进行序列化。

字符串字面量要用引号包裹，双引号或者单引号均可

* 字符串拼接

  加号可以用来拼接多个字符串或变量值、concat()方法

* 不可变性

  字符串不可变是指字符串的成员函数不会改变其原始值，而是创建并返回一个新的字符串。

* length属性

  > 如果字符串中包含双字节字符，那么length属性可能不会精确地返回字符串中的字符数目。
  >

#### 常用方法

| 方法           | 功能                     |
| -------------- | ------------------------ |
| charAt()       | 得到指定位置字符         |
| substring(a,b) | 提取[a,b)的子串          |
| substr(a,b)    | 提取从a开始b长度子串     |
| slice(a,b)     | 提取[a,b)子串            |
| toUpperCase()  | 变为大写                 |
| toLowerCase()  | 变为小写                 |
| indexOf()      | 检索字符串               |
| replace()      | 替换第一个匹配的内容     |
| includes()     | 判断是否包含特定字符 es6 |
| padStart()     | 头部补全字符串 es6       |
| padEnd()       | 尾部补全字符串 es6       |
| trim()         | 清除字符串首尾空格 es6   |
| replaceAll()   | 替换所有匹配内容 es6     |

  * 切片方法辨析

    substring(a,b)，如果a=b或者a大于最大位置，返回空字符串，a可以大于b，数字顺序将自动调整升序

    当某个参数是负值时，slice()方法将所有负值参数都当成字符串长度加上负参数值；substr()方法将第一个负参数值当成字符串长度加上该值，将第二个负参数值转换为0；substring()方法会将所有负参数值都转换为0。

  * includes()

    第一个参数是待搜索字符串，第二个参数是开始搜索的位置，默认从0开始。

  * padStart、padEnd()

    第一个参数是最大长度，第二个参数是用于补全的字符串，默认空格。

    如果原字符串的长度，等于或大于最大长度，不会消减原字符串，字符串补全不生效，返回原字符串

    用来补全的字符串与原字符串长度之和超过了最大长度，截去超出位数的补全字符串，原字符串不动

  * 首尾空格清除

    trimStart()、trimLeft()都用于去除首空格，trimEnd()、trimRight()用于去除尾空格
    
  * 替换方法

    replaceAll方法接收两个参数searchValue和replacement。searchValue表示搜索模式，可以字符串或全局的正则表达式，replacement是待替换的文本，可以使用`$1`获取匹配的组。

    如果replaceAll的搜索模式是一个不带g修饰符的正则表达式，则replaceAll会报错。


### 布尔类型

布尔型值只有两个: true和false，分别表示真和假

### undefined和null

null和undefined类型都只有一个值。undifined代表未定义，null表示空对象。变量声明后未初始化值为undifined，null需要主动赋值。

undifined不是js的保留字，所以可以用作变量名，通过void 0可以获得安全的undifined。

### Symbol

Symbol值可以通过Symbol()、Symbol.for()函数进行创建，通过Symbol()创建的值是唯一的，Symbol.for()会在全局作用域里查找有该标识的Symbol，如果不存在会创建一个Symbol。

Symbol的使用场景

1. 作为内置属性名称，避免同属性名覆盖
2. 使用Symbol代替常量
3. 作为私有变量

## 强制类型转换

JavaScript中的强制类型转换总是返回基本类型值。

**ToPrimitive**

ToPrimitive是一个抽象操作，将非基本类型的值转换为基本类型。该操作会调用valueOf或toString方法，如果这两个方法都不存在，会产生TypeError错误

### 显式类型转换

通过javascript提供的方法，实现数据类型的转换，这种方式就称为显式类型转换。

#### 转为数字类型

可以使用Number()函数将其余数据类型转换为数字类型。

* 字符串->数字

  * Number()函数能将纯数字字符串将被转换为数字，如果不是纯数字字符串将返回NaN，空字符串转为0。

  * 在数字字符串前添加`+`实现的效果与Number()函数的作用相同。

  * parseInt()函数能将字符串转换为整数。如果传入的参数不是字符串，会被隐式转换为字符串。

    parselnt()将自动截掉第一个非数字字符之后的所有字符，如果字符串不是以数字开头，则转换为NaN。可以为其指定第二个参数，用于解析其余进制的字符串。

  * parseFloat()函数能将字符串转换为浮点数。parseFloat()将自动截掉第一个非数字字符、非小数点之后的所有字符，如果字符串不是以数字开头，则转换为NaN。parseFloat()只解析十进制值，十六进制数值始终会返回0。

* 布尔->数字

  true将被转换为1，false为0

* undefined->数字

  结果为NaN

* null->数字

  结果为0
  
* object->数字

  对象会首先通过ToPrimitive被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其强制转换为数字。

#### 转为字符串

可以使用String()函数或者toString()方法将其余数据类型转换为字符串类型。

* 数字->字符串

  变为“长得相同”的字符串。科学计数法和非10进制数字会转为10进制的值。

  在调用数值的toString()方法时，可以传递一个参数表示输出数值的基数。

* 布尔值->字符串

  变为“长得相同”的字符串

* undefined、null->字符串

  变为“长得相同”的字符串

#### 转为布尔值

其他类型值可以通过Boolean()方法或`!!`运算符转换为布尔值。

**假值**

假值的布尔强制类型转换结果为false。假值列表有undefined、null、false、+0、-0、NaN、‘’“。

**真值**

除假值列表外的值强制类型转换都为true。

### 隐式强制类型转换

隐式强制类型转换指的是那些隐蔽的强制类型转换。

#### 字符串

`+`运算符即能用于数字加法，也能用于字符串拼接。用于字符串拼接时会发生隐式强制类型转换。

如果`+`的其中一个操作数是字符串或者能够通过如下步骤能转换为字符串，`+`将执行拼接操作。使用ToPrimitive操作后再使用[[DefaultValue]]，以数字作为上下文。a + ""会对a调用valueOf()方法，然后通过ToString抽象操作将返回值转换为字符串。

#### 数字

如果`-`、`/`等数字运算符的操作数不是数字，那么将会发生隐式强制类型转换，这些操作数会强制类型转换为字符串后，再转换为数字，如果操作数是布尔值，那么true和false会分别转换为1和0。

#### 布尔值

布尔值隐式强制类型转换发生在条件判断表达式中，如if、for、while、||等，非布尔值会被强制类型转换为布尔值，转换规则同Boolean()方法。

>[] +{}//[object object]
>
>{} + [] //0 {}被当做空代码块，如果使用({})替换，则结果与上一行相同

## 原生函数

JavaScript为基本数据类型值提供了封装对象，称为原生函数（如String、Number、Boolean等）。它们为基本数据类型值提供了该子类型所特有的方法和属性。当读取一个基本类型值的属性时，JavaScript引擎会自动对该值进行封装来实现对这些属性和方法的访问，创建一个对应的基本包装类型的对象，从而让我们能够在该对象上调用方法，方法调用结束后台会销毁该对象。

基本包装类创建

* 显式调用基本包装类构造函数创建基本包装类对象。
* Object构造函数传入基本类型会返回对应的基本包装类

对基本包装类型的实例调用typeof会返回"object"，而且所有基本包装类型的对象在转换为布尔类型时值都是true。

包装类对象的PrimitiveValue属性存储它们的本身值。
