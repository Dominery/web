# 数据类型

计算机程序是通过操作值来运行的，数据类型是值的一个属性，它定义了值的行为，描述在该值上允许执行的操作，以使其区别于其他值。

在ECMAScript 2015规范中定义了七种内置数据类型：number、string、boolean、undifined、null、symbol、object。除object外，其余类型都属于基本数据类型。

[ES2020](https://github.com/tc39/proposal-bigint) 引入了一种新的数据类型 BigInt。

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
| bigint    | bigint         |

> 基本数据类型存储在栈中，变量赋值时采用深拷贝，引用数据类型存储在堆里，变量赋值时复制了引用指针，引用指针指向堆里的对象的地址。

**字面量**

在计算机科学中，字面量用于在源代码中表示某个固定值。在JavaScript程序中，字面量不是变量，它是直接给出的固定值。

## 基本数据类型

### 数字

JavaScript使用双精度64位浮点数格式（IEEE 754）来表示数字，因此所有数字本质上都是浮点数。IEE 754规定1位符号位、11位指数位、52位尾数位。

#### 数值范围

js中整数使用尾数位进行表示，因此最大整数为2^53^-1(尾数有一个隐藏位)。如果对一个超过最大整数的数进行加减操作，结果会因为浮点数表示范围限制而存在不正确现象。

如果一个数大于等于2的1024次方，那么就会发生“正向溢出”，这时就会返回`Infinity`。

如果一个数小于等于2的-1075次方（指数部分最小值-1023，再加上小数部分的52位），那么就会发生为“负向溢出”，这时会直接返回0。

JavaScript 提供`Number`对象的`MAX_VALUE`和`MIN_VALUE`属性，返回可以表示的具体的最大值和最小值。

#### 数字的表示

表达无整数位的小数时，可以省略整数位。

##### 科学计数法

js如果遇到下述情况会自动将数值转为科学计数法。

* 小数点前数字多于21位
* 小数点后连续0多于5个

##### 进制表示

es5提供了十六进制的数字表示，以0x开头，es6提供了二进制数值及八进制的数字表示，分别以0b开头、以0o开头。

如果进制数值中出现不属于该进制的字符，则会报错。

使用Number()方法可以将不同进制字符串值转换为10进制。

##### 数字分隔符

[ES2021](https://github.com/tc39/proposal-numeric-separator)，允许 JavaScript 的数值使用下划线（`_`）作为分隔符。这个数值分隔符没有指定间隔的位数，也就是说，可以每三位添加一个分隔符，也可以每一位、每两位、每四位添加一个。

- 不能放在数值的最前面（leading）或最后面（trailing）。
- 不能两个或两个以上的分隔符连在一起。
- 小数点的前后不能有分隔符。
- 科学计数法里面，表示指数的`e`或`E`前后不能有分隔符。
- 分隔符不能紧跟着进制的前缀`0b`、`0B`、`0o`、`0O`、`0x`、`0X`。

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

* `toExponential`方法的参数是小数点后有效数字的位数，范围为0到100，超出这个范围，会抛出一个 RangeError 错误。

##### toLocaleString()

接受一个地区码作为参数，返回一个字符串，表示当前数字在该地区的当地书写形式。

可以接受第二个参数配置对象，用来定制指定用途的返回字符串。该对象的`style`属性指定输出样式，默认值是`decimal`，表示输出十进制形式。如果值为`percent`，表示输出百分数。

* 该方法如果使用浏览器不认识的地区码，会抛出一个错误。

```javascript
(123).toLocaleString('zh-Hans-CN-u-nu-hanidec')
// "一二三"

(123).toLocaleString('zh-Hans-CN', { style: 'currency', currency: 'CNY' })
// "￥123.00"

(123).toLocaleString('de-DE', { style: 'currency', currency: 'EUR' })
// "123,00 €"
```

#### 静态方法

##### Number.isFinite(), Number.isNaN()——es6

ES6 在`Number`对象上，新提供了`Number.isFinite()`和`Number.isNaN()`两个方法。

`Number.isFinite()`用来检查一个数值是否为有限的（finite），即不是`Infinity`。

`Number.isNaN()`用来检查一个值是否为`NaN`。

它们与传统的全局方法`isFinite()`和`isNaN()`的区别在于，传统方法先调用`Number()`将非数值的值转为数值，再进行判断，而这两个新方法只对数值有效，`Number.isFinite()`对于非数值一律返回`false`, `Number.isNaN()`只有对于`NaN`才返回`true`，非`NaN`一律返回`false`。

**Infinity**

非零数字除以0时，结果是Infinity或-Infinity

如果某次计算的结果得到了一个超出JavaScript数值范围的值，那么这个数值将被自动转换成特殊的Infinity值。

`Number.NEGATIVE_INFINITY`和`Number.POSITIVE_INFINITY`可以得到负和正Infinity的值。

**NaN**

NaN是英语“not a number”的意思，即“不是一个数”，却是一个数字类型的值

0除以0、0乘以Infinity、Infinity相减相除的结果是NaN，事实上，在数学运算中，若结果不能得到数字，其结果往往都是NaN。

##### Number.parseInt(), Number.parseFloat()——es6

ES6 将全局方法`parseInt()`和`parseFloat()`，移植到`Number`对象上面，行为完全保持不变。

**parseInt**

`parseInt`方法用于将字符串转为整数。

`parseInt`方法还可以接受第二个参数（2到36之间），表示被解析的值的进制，返回该值对应的十进制数。默认情况下，`parseInt`的第二个参数为10，即默认是十进制转十进制。

字符串转为整数的时候，是一个个字符依次转换，如果遇到不能转为数字的字符，就不再进行下去，返回已经转好的部分。

* 如果字符串头部有空格，空格会被自动去除。
* 如果`parseInt`的参数不是字符串，则会先转为字符串再转换。
* 如果字符串的第一个字符不能转化为数字（后面跟着数字的正负号除外），返回`NaN`。
* 如果字符串以`0x`或`0X`开头，`parseInt`会将其按照十六进制数解析。
* 如果字符串以`0`开头，将其按照10进制解析。
* `parseInt`会将科学计数法的表示方法视为字符串
* 如果第二个参数不是数值，会被自动转为一个整数。这个整数只有在2到36之间，才能得到有意义的结果，超出这个范围，则返回`NaN`。
* 如果第二个参数是`0`、`undefined`和`null`，则直接忽略。

**parseFloat**

`parseFloat`方法用于将一个字符串转为浮点数。

`parseFloat`方法会自动过滤字符串前导的空格。

* 如果字符串符合科学计数法，则会进行相应的转换。
* 如果字符串包含不能转为浮点数的字符，则不再进行往后转换，返回已经转好的部分。
* 如果参数不是字符串，则会先转为字符串再转换。
* 如果字符串的第一个字符不能转化为浮点数，则返回`NaN`。`parseFloat`会将空字符串转为`NaN`。

##### Number.isInteger()

`Number.isInteger()`用来判断一个数值是否为整数。

* 如果一个数值的绝对值小于`Number.MIN_VALUE`（5E-324），即小于 JavaScript 能够分辨的最小值，会被自动转为 0。这时，`Number.isInteger`也会误判。

##### Number.EPSILON

ES6 在`Number`对象上面，新增一个极小的常量`Number.EPSILON`。根据规格，它表示 1 与大于 1 的最小浮点数之间的差。

对于 64 位浮点数来说，大于 1 的最小浮点数相当于二进制的`1.00..001`，小数点后面有连续 51 个零。这个值减去 1 之后，就等于 2 的 -52 次方。

引入一个这么小的量的目的，在于为浮点数计算，设置一个误差范围。

##### Number.isSafeInteger()

JavaScript 能够准确表示的整数范围在`-2^53`到`2^53`之间（不含两个端点），超过这个范围，无法精确表示这个值。

ES6 引入了`Number.MAX_SAFE_INTEGER`和`Number.MIN_SAFE_INTEGER`这两个常量，用来表示这个范围的上下限。

`Number.isSafeInteger()`则是用来判断一个整数是否落在这个范围之内

### BigInt

JavaScript 所有数字都保存成 64 位浮点数，这给数值的表示带来了两大限制。

1. 数值的精度只能到 53 个二进制位
2. 无法表示大于或等于2的1024次方的数值

BigInt数据类型可以解决这个问题，BigInt只用来表示整数，没有位数的限制，任何位数的整数都可以精确表示。

#### 构建

##### 字面量

* 为了与 Number 类型区别，BigInt 类型的数据必须添加后缀`n`。
* BigInt 同样可以使用各种进制表示，都要加上后缀`n`。
* `typeof`运算符对于 BigInt 类型的数据返回`bigint`。
* BigInt 与普通整数是两种值，它们之间并不相等。
* BigInt 数字前可以使用负号（`-`），但是不能使用正号（`+`），因为会进行数字转换，而这无法转换。

##### BigInt函数

JavaScript 原生提供`BigInt`函数，可以用它生成 BigInt 类型的数值。转换规则基本与`Number()`一致，将其他类型的值转为 BigInt，如果不能转换成整数，会报错。

#### 运算

BigInt 类型的`+`、`-`、`*`和`**`这四个二元运算符，与 Number 类型的行为一致。除法运算`/`会舍去小数部分，返回一个整数。

下面两个运算符在BigInt上会报错。

- 不带符号的右移位运算符`>>>`
- 一元的求正运算符`+`

BigInt 不能与普通数值进行混合运算

#### 转为Number

使用BigInt的实例方法toString()能够转为Number类型，如果传入数值参数，会转为该进制的数。

#### 静态方法

##### BigInt.asUintN(width, BigInt)

给定的 BigInt 转为 0 到 2^width^ - 1 之间对应的值，返回值为BigInt

##### BigInt.asIntN(width, BigInt)

给定的 BigInt 转为 -2^width^ - 1 到 2^width-1^  - 1 之间对应的值，返回值为BigInt

如果`BigInt.asIntN()`和`BigInt.asUintN()`指定的位数，小于数值本身的位数，那么头部的位将被舍弃。

##### BigInt.parseInt(string[, radix])

暂无该方法，近似于`Number.parseInt()`，将一个字符串转换成指定进制的 BigInt

### 字符串

字符串字面量要用引号包裹，双引号或者单引号均可

* 字符串拼接

  加号可以用来拼接多个字符串或变量值、concat()方法

* 不可变性

  字符串不可变是指字符串的成员函数不会改变其原始值，而是创建并返回一个新的字符串。

* length属性

  > 如果字符串中包含双字节字符，那么length属性可能不会精确地返回字符串中的字符数目。

#### 模板字符串

传统的 JavaScript 语言，输出模板语法不方便，ES6 引入了模板字符串解决这个问题。

模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

* 如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中。
* 模板字符串中嵌入变量，需要将变量名写在`${}`之中。

#### JSON

JSON.stringify()和JSON.parse()可以将js对象序列化和反序列化，如果一个对象中存在循环引用、undifined、symbol、function等不符合JSON结构的值，那么JSON.stringify()方法不能正常执行，循环引用会报错，其余情况会被忽略，在数组中返回null。

如果对象中定义了toJSON()方法，JSON字符串化时会首先调用该方法，然后用它的返回值来进行序列化。

#### Base64转码

所谓 Base64 就是一种编码方法，可以将任意值转成 0～9、A～Z、a-z、`+`和`/`这64个字符组成的可打印字符。使用它的主要目的，不是为了加密，而是为了不出现特殊字符，简化程序的处理。

JavaScript 原生提供两个 Base64 相关的方法。

- `btoa()`：任意由单字节组成的字符串转为 Base64 编码
- `atob()`：Base64 编码转为原来的值

这两个方法不适合非 ASCII 码的字符，会报错。

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
| repeat(val)    | 原字符串重复val次        |

##### substring(), slice(), substr()

substring(a,b)，如果a=b或者a大于最大位置，返回空字符串，a可以大于b，数字顺序将自动调整升序

当某个参数是负值时，slice()方法将所有负值参数都当成字符串长度加上负参数值；substr()方法将第一个负参数值当成字符串长度加上该值，将第二个负参数值转换为0；substring()方法会将所有负参数值都转换为0。

如果第一个参数经计算后依旧为负值，那么会被转换为0。

##### includes(), strartsWith(), endsWith()——es6

- includes()：返回布尔值，表示是否找到了参数字符串。
- startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
- endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。

这三个方法都支持第二个参数，表示开始搜索的位置

##### localeCompare()

用于比较两个字符串。它返回一个整数，如果小于0，表示第一个字符串小于第二个字符串；如果等于0，表示两者相等；如果大于0，表示第一个字符串大于第二个字符串。

该方法的最大特点，就是会考虑自然语言的顺序。

`localeCompare`还可以有第二个参数，指定所使用的语言（默认是英语），然后根据该语言的规则进行比较。

##### charAt(pos)

返回指定位置的字符，参数是从`0`开始编号的位置。

如果参数为负数，或大于等于字符串的长度，`charAt`返回空字符串。

##### at(pos)

`at()`方法接受一个整数作为参数，返回参数指定位置的字符，支持负索引（即倒数的位置）。

如果参数位置超出了字符串范围，`at()`返回`undefined`。

#### 转换

##### concat()

`concat`方法用于连接两个字符串，返回一个新字符串，不改变原字符串。可以接受多个参数。

如果参数不是字符串，`concat`方法会将其先转为字符串，然后再连接。

##### repeat(val)

返回新字符串，值为原有字符串重复val次

* 如果传入小数，会被取整
* 如果传入小于-1的负数或Infinity，报错
* 如果传入NaN，等同0
* 如果传入其他类型，转换成数字，如果无法转换成数字视同0

##### toLowerCase(), toUpperCase()

`toLowerCase`方法用于将一个字符串全部转为小写，`toUpperCase`则是全部转为大写。它们都返回一个新字符串，不改变原字符串。

##### split()

`split`方法按照给定规则分割字符串，还可以使用正则表达式作为参数，返回一个由分割出来的子字符串组成的数组。

`split`方法还可以接受第二个参数，限定返回数组的最大成员数。

* 如果分割规则为空字符串，则返回数组的成员是原字符串的每一个字符。
* 如果省略参数，则返回数组的唯一成员就是原字符串。
* 如果满足分割规则的两个部分紧邻着（即两个分割符中间没有其他字符），则返回数组之中会有一个空字符串。
* 如果满足分割规则的部分处于字符串的开头或结尾（即它的前面或后面没有其他字符），则返回数组的第一个或最后一个成员是一个空字符串。

##### **padStart(val, str), padEnd(val, str)**——es2017

返回新字符串，长度为max(val, 原有字符串长度)，padStart在原字符串前填充str，padEnd在后。

* 如果val <= 原有字符串长度，返回原字符串
* 如果str与原字符串，两者的长度之和超过了最大长度，则会截去超出位数的str
* 如果省略第二个参数，默认使用空格补全长度

##### trim()

`trim`方法用于去除字符串两端的空格，返回一个新字符串，不改变原字符串。

该方法去除的不仅是空格，还包括制表符（`\t`、`\v`）、换行符（`\n`）和回车符（`\r`）。

##### **trimStart(), trimEnd()**——es2019

返回消除空格的字符串，`trimStart()`消除字符串头部的空格，`trimEnd()`消除尾部的空格。

`trimLeft()`是`trimStart()`的别名，`trimRight()`是`trimEnd()`的别名。

##### String.raw()——es6

ES6 还为原生的 String 对象，提供了一个`raw()`方法。该方法返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串，往往用于模板字符串的处理方法。

`String.raw()`本质上是一个正常的函数，只是专用于模板字符串的标签函数。

#### 匹配

##### match()

`match`方法用于确定原字符串是否匹配某个子字符串，返回一个数组，成员为匹配的第一个字符串。如果没有找到匹配，则返回`null`。

返回的数组还有`index`属性和`input`属性，分别表示匹配字符串开始的位置和原始字符串。

如果匹配的正则表达式是global模式，则返回一个数组，成员是匹配的字符串。

##### matchAll()

返回一个正则表达式在当前字符串的所有匹配的迭代器。迭代元素是一个数组，数组的第一个元素为匹配的字符串，之后的元素为组匹配，数组有index属性表示开始匹配的位置，input属性表示原字符串。

##### **replaceAll(searchValue, replacement)**——es2021

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

#### 编码

JavaScript使用UTF-16编码来表示一个字符。UTF-16编码以两个字节作为一个编码单元，每个字符使用一个编码单元或者两个编码单元来表示。

##### String.fromCharCode()

该方法的参数是一个或多个数值，代表 Unicode 码点，返回值是这些码点组成的字符串。

该方法不支持 Unicode 码点大于`0xFFFF`的字符，即传入的参数不能大于`0xFFFF`，否则会忽略多出的位。

##### String.fromCodePoint()

ES5 提供`String.fromCharCode()`方法，用于从 Unicode 码点返回对应字符，但是这个方法不能识别码点大于`0xFFFF`的字符。

ES6 提供了`String.fromCodePoint()`方法，可以识别大于`0xFFFF`的字符，弥补了`String.fromCharCode()`方法的不足。

如果`String.fromCodePoint`方法有多个参数，则它们会被合并成一个字符串返回。

`fromCodePoint`方法定义在`String`对象上，与之作用相反的`codePointAt`方法定义在字符串的实例对象上。

##### charCodeAt()

返回字符串指定位置的 Unicode 码点（十进制表示），相当于`String.fromCharCode()`的逆操作。

如果参数为负数，或大于等于字符串的长度，`charCodeAt`返回`NaN`。

`charCodeAt`方法返回的 Unicode 码点不会大于65536（0xFFFF）

##### codePointAt()

JavaScript 内部，字符以 UTF-16 的格式储存，每个字符固定为`2`个字节。对于那些需要`4`个字节储存的字符（Unicode 码点大于`0xFFFF`的字符），JavaScript 会认为它们是两个字符。

ES6 提供了`codePointAt()`方法，能够正确处理 4 个字节储存的字符，返回一个字符的码点。参数是字符在字符串中的位置，位置识别可以通过for ... of 或者拓展运算符(...)解决。

##### normalize()

ES6 提供字符串实例的`normalize()`方法，用来将字符的不同表示方法统一为同样的形式，这称为 Unicode 正规化。

### 布尔类型

布尔型值只有两个: true和false，分别表示真和假

### undefined和null

null和undefined类型都只有一个值。undifined代表未定义，null表示空对象。变量声明后未初始化值为undifined，null需要主动赋值。

undifined不是js的保留字，所以可以用作变量名，通过void 0可以获得安全的undifined。

### Symbol

ES5 的对象属性名都是字符串，这容易造成属性名的冲突。ES6 引入`Symbol`，保证每个属性的名字都是独一无二的。

注意

* `Symbol()`函数前不能使用`new`命令，否则会报错。
* 由于 Symbol 值不是对象，所以也不能添加属性
* Symbol 值不能与其他类型的值进行运算，会报错。
* Symbol 值也可以转为布尔值，但是不能转为数值。

#### 创建

Symbol值可以通过Symbol()、Symbol.for()函数进行创建，通过Symbol()创建的值是唯一的。对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的 Symbol 类型。

##### Symbol()

`Symbol()`函数可以接受一个字符串作为参数，表示对 Symbol 实例的描述。如果 Symbol 的参数是一个对象，就会调用该对象的`toString()`方法。

##### Symbol.for()

`Symbol.for()`方法接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建一个以该字符串为名称的 Symbol 值，并将其注册到全局。

#### 使用

##### description

`Symbol()`函数创建 Symbol 值时，可以用参数添加一个描述。这个描述可以通过`String()`函数或`toString()`方法获取。[ES2019](https://github.com/tc39/proposal-Symbol-description) 提供了一个 Symbol 值的实例属性`description`，直接返回 Symbol 值的描述。

##### 作为属性名

只要 Symbol 值作为标识符，用于对象的属性名，就能保证不会出现同名的属性。

* Symbol 值作为对象属性名时，不能用点运算符。
* 在对象的内部，使用 Symbol 值定义属性时，Symbol 值必须放在方括号之中。
* 可以用于定义一组常量，保证这组常量的值都是不相等的。

##### 属性名遍历

Symbol 值作为属性名，遍历对象的时候，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。

`Object.getOwnPropertySymbols()`方法，可以获取指定对象的所有 Symbol 属性名。该方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。

`Reflect.ownKeys()`方法可以返回所有类型的键名，包括常规键名和 Symbol 键名。

##### Symbol.keyFor()

`Symbol.keyFor()`方法返回一个已登记的 Symbol 类型值的`key`，如果没有相应的symbol返回undefined。

#### 内置Symbol

| 属性                      | 说明                                         |
| ------------------------- | -------------------------------------------- |
| Symbol.hasInstance        | 指向内部方法，`instanceof`时调用             |
| Symbol.isConcatSpreadable | 布尔值，使用`concat()`是否展开               |
| Symbol.species            | 指向构造函数，创建衍生对象时调用             |
| Symbol.match              | 指向函数，使用str.match()调用                |
| Symbol.replace            | 指向方法，使用str.replace()调用              |
| Symbol.search             | 指向方法，使用str.search()调用               |
| Symbol.split              | 指向方法，使用str.split()调用                |
| Symbol.iterator           | 指向默认遍历器方法                           |
| Symbol.toPrimitive        | 指向方法，该对象被转为原始类型的值时，会调用 |
| Symbol.toStringTag        | 指向方法，Object.prototype.toString()时调用  |

##### Symbol.species

对象的`Symbol.species`属性，指向一个构造函数。创建衍生对象时，会使用该属性。定义了`Symbol.species`属性，创建衍生对象时就会使用这个属性返回的函数，作为构造函数。

```javascript
// 默认
static get [Symbol.species]() {
    return this;
}
```

##### Symbol.toPrimitive

`Symbol.toPrimitive`被调用时，会接受一个字符串参数，表示当前运算的模式，一共有三种模式。

- number：该场合需要转成数值
- string：该场合需要转成字符串
- default：该场合可以转成数值，也可以转成字符串

##### Symbol.toStringTag

在该对象上面调用`Object.prototype.toString`方法时，如果这个属性存在，它的返回值会出现在`toString`方法返回的字符串之中，表示对象的类型。也就是说，这个属性可以用来定制`[object Object]`或`[object Array]`中`object`后面的那个字符串。

## 强制类型转换

JavaScript中的强制类型转换总是返回基本类型值。

**ToPrimitive**

ToPrimitive是一个抽象操作，将非基本类型的值转换为基本类型。该操作会调用valueOf或toString方法获取基本类型，如果这两个方法都不存在或无法返回基本类型，会产生TypeError错误

如果转换为数字，先valueof，后toString；如果转换为string，先toString，后valueof。

### 显式类型转换

通过javascript提供的方法，实现数据类型的转换，这种方式就称为显式类型转换。

#### 转为数字类型

可以使用Number()函数将其余数据类型转换为数字类型。

* 字符串->数字

  * Number()函数能将纯数字字符串将被转换为数字，会过滤前导和后缀的空格，如果不是纯数字字符串将返回NaN，空字符串转为0。

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

  对象会首先通过ToPrimitive操作获取基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其强制转换为数字。

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
