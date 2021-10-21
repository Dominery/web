# 内置对象

内置对象是由ECMAScript实现提供的、不依赖于宿主环境的对象，这些对象在ECMAScript程序执行之前就已经存在了。Object、Array、String等都是内置对象，除此之外还有两个单体内置对象：Global和Math。

## Global对象

不属于任何其他对象的属性和方法，最终都是它的属性和方法。事实上，没有全局变量或全局函数；所有在全局作用域中定义的属性和函数，都是Global对象的属性。诸如isNaN()、isFinite()、parseInt()以及parseFloat()。

### 方法

#### URI编码方法

Global对象的encodeURI()和encodeURIComponent()方法可以对URI（Uniform Resource Identifiers，通用资源标识符）进行编码。decodeURI()和decodeURIComponent()可以对编码过的URI进行解码。

encodeURI()主要用于整个URI（例如，http://www.wrox.com/illegal_value.htm），而encodeURIComponent()主要用于对URI中的某一段（例如前面URI中的illegal value.htm）进行编码。它们的主要区别在于，encodeURI()不会对本身属于URI的特殊字符进行编码，例如冒号、正斜杠、问号和井字号；而encodeURIComponent()则会对它发现的任何非标准字符进行编码。

decodeURI()仅能够解码使用encodeURI()编码后的字符，decodeURIComponent()可以解码任何特殊字符的编码。

#### eval()方法

eval()方法接收JavaScript代码字符串，并执行。

当解析器发现代码中调用eval()方法时，它会将传入的参数当作实际的ECMAScript语句来解析，然后把执行结果插入到原位置。

在eval()中创建的任何变量或函数都不会被提升，因为在解析代码的时候，它们被包含在一个字符串中；它们只在eval()执行的时候创建。

严格模式下，在外部访问不到eval()中创建的任何变量或函数。

### 属性

JavaScript中使用的全局变量都是Global对象的属性。如undefined、NaN、infinity等特殊值，Object、Function等构造函数。

下面的表格包含了Global对象所有属性。

![](../../../images/global.png)

### window对象

前端js中，浏览器将Global对象作为window对象的一部分加以实现。在全局作用域上声明的变量和方法都是window对象的属性。

## Math对象

ECMAScript还为保存数学公式和信息提供了一个公共位置，即Math对象。

### 属性

| 属性    | 说明            |
| ------- | --------------- |
| E       | 自然对数的底数  |
| LN10    | 10的自然对数    |
| LN2     | 2的自然对数     |
| LOG2E   | 以2位底e的对数  |
| LOG10E  | 以10为底e的对数 |
| SQRT1_2 | 1/2的平方根     |
| SQRT2   | 2的平方根       |

### 方法

| 方法     | 说明                   |
| -------- | ---------------------- |
| min()    | 确定一组数值中的最小值 |
| max()    | 确定一组数值中最大值   |
| ceil()   | 向上舍入               |
| floor()  | 向下舍入               |
| round()  | 四舍五入               |
| random() | 返回[0,1)的随机数      |

除了上面的方法，Math对象还存在着一些数学计算方法。