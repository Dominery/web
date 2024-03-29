# 运算符

## 基本运算符

### 算术运算符

| 运算符 | 意义       |
| ------ | ---------- |
| +      | 加         |
| -      | 减         |
| *      | 乘         |
| /      | 除         |
| %      | 取余       |
| **     | 指数       |
| ++     | 自增       |
| --     | 自减       |
| +x     | 数值运算符 |
| -x     | 负数值运算 |

默认情况，乘除法的优先级要高于加法和减法;必要时可以使用圆括号来改变运算的顺序。

* 加号的两种作用

  如果加号两边的操作数都是数字，则为“加法”，否则为连字符

* 取余运算

  a % b表示求a除以b的余数。取余运算中，如果b为负数，则其结果与b为正数时相同。结果的正负只与a有关。a、b可以是浮点数。

* IEEE754

  JavaScript使用了IEEE754二进制浮点数算术标准，这会使一些个别的小数运算产生“丢失精度”问题。

  解决办法:在进行小数运算时，要调用数字的toFixed()方法保留指定的小数位数

* 幂和开根号

  JavaScript中没有提供幂计算、开根号的运算符。需要使用Math对象的pow和sqrt方法进行计算。如果对负数进行开根运算，返回值为NaN。

* 取整

  Math.ceil()向上取整; Math.floor()向下取整

### 关系运算符

| 运算符 | 意义     |
| ------ | -------- |
| >      | 大于     |
| <      | 小于     |
| >=     | 大于等于 |
| <=     | 小于等于 |
| ==     | 等于     |
| !=     | 不等于   |
| ===    | 全等于   |
| !==    | 不全等于 |

* 连续比较

  js中变量不能像python一样进行连续比较，需要通过逻辑运算符隔开，如果书写连续比较，则会按照顺序计算。

#### 非相等比较

引用类型的值非相等比较和字符串间比较一样都是字典序比较。

**字符串**

字符串使用非相等运算符会按照字典序比较，JavaScript 引擎内部首先比较首字符的 Unicode 码点。如果相等，再比较第二个字符的 Unicode 码点，以此类推。

**非字符串**

如果两个运算子都是原始类型的值，则是先转成数值再比较。

如果运算子是对象，会转为原始类型的值，再进行比较。

#### 相等比较

基本类型进行相等判断时，会比较值是否相等

引用类型进行相等判断时，会比较址是否相等，也就是说它会比较是否为内存中的同一个东西

**相等与全等**

两个等号运算符不比较值的类型，它会进行隐式转换，将值转换为同一类型后，比较值是否相等

> null和undefined用==进行比较涉及隐式强制类型转换，此两者比较后值相等。
>
> NaN作为一个特殊的数字类型值，它不自等

三个等号=运算符，不仅比较值是否相同，也比较类型是否相同

**相等运算不同类型转换规则**

1. 判断是否为null和undefined，如果是，返回true
2. 判断是否为number和string，如果是，将string转换为number进行比较
3. 判断是否有boolean，如果是，将boolean转换为number进行比较
4. 判断是否object，且另一方为string、number、symbol，如果是，将object转换为number后返回第二步进行比较
5. 返回false

#### 引用值相等函数

下面这个函数可以判断两个引用类型的值是否相等。

```js
function deepEqual(value1,value2){
    if(value1===value2)return true;
    else if(value1===null || value2===null)return false;
    else if(typeof value1==="object" && typeof value2==="object")return objAttrEqual(value1,value2);
    else return false;
    
    function objAttrEqual(objA,objB){
        const aKeys = new Set(Object.keys(objA)), bKeys = new Set(Object.keys(objB));
        if (aKeys.length != bKeys.length) return false;
        
        for (let key of aKeys) {
            if (!bKeys.has(key) || !deepEqual(objA[key], objB[key])) return false;
        }
        return true;
	}
}
```

### 布尔运算符

| 运算符 | 意义       |
| ------ | ---------- |
| !      | 非         |
| &&     | 与         |
| \|\|   | 或         |
| ?:     | 三元运算符 |

与或运算返回的不一定是布尔值，而是返回参与运算的一个值。

非运算返回的是布尔值。

* 短路计算

  a && b运算中:a真，表达式值为b;a假，表达式值为a

  a || b运算中: a真，表达式值为a; a假，表达式值为b

* 运算顺序

  非 > 与 > 或

以下六个值为假值，在布尔运算中作为运算子，值为false。

- `undefined`
- `null`
- `false`
- `0`
- `NaN`
- 空字符串（`''`）

### 赋值运算符

| 运算符             | 意义     |
| ------------------ | -------- |
| =                  | 赋值     |
| +=、-=、*=、/=、%= | 快捷赋值 |
| ++                 | 自增运算 |
| --                 | 自减运算 |

赋值操作都会产生值

1. 等号赋值后面的值将作为返回值。因此可以连续对多个变量赋值。
2. 快速赋值已经运算后的变量值将作为返回值。
3. 自增自减运算如果在变量后面，返回未运算的值，如果在变量前面，返回运算后的值

```javascript
var a,b;
a = b = 5; //a=5,b=5
a += b+= 5; //b=10,a=15
b = a++; // b=15,a=16
a = ++b;// a= 16, b=16
```

ES2021 引入了三个新的[逻辑赋值运算符](https://github.com/tc39/proposal-logical-assignment)`||=`、`&&=`、`??=`，将逻辑运算符与赋值运算符进行结合。它们可以为变量或属性设置默认值。

```javascript
// 或赋值运算符
x ||= y
// 等同于
x || (x = y)

// 与赋值运算符
x &&= y
// 等同于
x && (x = y)

// Null 赋值运算符
x ??= y
// 等同于
x ?? (x = y)
```

### 位运算符

| 运算符 | 意义                 |
| ------ | -------------------- |
| ~      | 取反                 |
| \|     | 取或                 |
| &      | 取与                 |
| ^      | 异或                 |
| <<     | 左移运算符           |
| >>     | 右移运算符           |
| >>>    | 头部补零的右移运算符 |

字位运算符只适用于32位整数，运算符会强制操作数使用32位格式。

如果想要对能用32位进行表示的实数截除小数位，可以使用`num|0`或者`~~num`、`num^0`。

对字符串进行二进制否运算，JavaScript 引擎会先调用`Number`函数，将字符串转为数值。

`~`运算结果只有-1返回假值，所以可以使用`~`代替`>-1`判断。

**左移运算**

左移运算符（`<<`）表示将一个数的二进制值向左移动指定的位数，尾部补`0`，即乘以`2`的指定次方。向左移动的时候，最高位的符号位是一起移动的。

如果左移0位，就相当于将该数值转为32位整数，等同于取整，对于正数和负数都有效。

**头部补零的右移运算**

头部补零的右移运算符（`>>>`）与右移运算符（`>>`）只有一个差别，就是一个数的二进制形式向右移动时，头部一律补零，而不考虑符号位。所以，该运算总是得到正值。

查看一个负整数在计算机内部的储存形式，最快的方法就是使用这个运算符。

`-1 >>> 0`-1为32位整数时，内部的储存形式使用无符号整数格式解读，值为 4294967295（即`(2^32)-1`）。

## 其他运算符

### void运算符

`void`运算符的作用是执行一个表达式，然后不返回任何值，或者说返回`undefined`。

`void`运算符的优先性很高，如果不使用括号，容易造成错误的结果。

### 逗号运算符

逗号运算符用于对两个表达式求值，并返回后一个表达式的值。

逗号运算符的一个用途是，在返回一个值之前，进行一些辅助操作。

### 可选链运算符

 [ES2020](https://github.com/tc39/proposal-optional-chaining) 引入了“链判断运算符”,`?.`，能够降低对象多层属性安全访问的复杂度。

可选链运算符特性：

1. 短路机制，如果可选链运算符左侧操作数的求值结果为undefined或null，那么右侧的操作数不会再被求值
2. `?.`后不能为数字，否则作为三元运算符

可选链运算符有以下三种语法形式：

* 可选的静态属性访问，如`obj.prop`
* 可选的计算属性访问，如`obj?.[expr]`
* 可选的函数调用或方法调用，如`fn?.()`

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

### 空值合并运算符

读取对象属性的时候，如果某个属性的值是`null`或`undefined`，有时候需要为它们指定默认值。常见做法是通过`||`运算符指定默认值。但是属性的值如果为空字符串或`false`或`0`，默认值也会生效。

[ES2020](https://github.com/tc39/proposal-nullish-coalescing) 引入了空值合并运算符，`??`，该运算符为二元运算符，当且仅当运算符左侧操作数类型为null或undifined，返回右侧操作数，否则返回左侧操作数。

* `??`本质上是逻辑运算，如果多个逻辑运算符一起使用，必须用括号表明优先级，否则会报错。
* 空值合并运算符目的就是跟链判断运算符`?.`配合使用

### 展开运算符

#### 展开数组

展开运算符可以将数组内元素迭代取出。

#### 构造字面量对象

对象不能直接使用展开运算符，只有在构造字面量对象即周围有花括号时，可以将原有对象的属性复制。React传props时展开运算符不是复制的作用，React结合babel实现展开对象的功能。

通过展开运算符可以将对象属性存放到空对象中，构成新对象。

可以通过对象展开合并多个对象的属性。es6新增Object.assign()方法用来合并对象，但是不构成新对象，将其余对象属性添加到第一个对象中。

* 如果展开空对象，没有任何效果
* 如果展开的不是对象，则会自动将其转为对象，再将其属性罗列出来
* 如果展开运算符后面是字符串，它会自动转成一个类似数组的对象，因此返回的不是空对象

**应用**

* 克隆对象
* 用户参数和默认参数

## 运算顺序

### 优先级

JavaScript 各种运算符的优先级别（Operator Precedence）是不一样的。优先级高的运算符先执行，优先级低的运算符后执行。

一般来说优先级由高到低，算术运算符、关系运算符、布尔运算符、赋值运算符。

#### 圆括号

圆括号（`()`）可以用来提高运算的优先级，因为它的优先级是最高的，即圆括号中的表达式会第一个运算。

圆括号不是运算符，而是一种语法结构。它一共有两种用法：一种是把表达式放在圆括号之中，提升运算的优先级；另一种是跟在函数的后面，作用是调用函数。

* 圆括号之中，只能放置表达式，如果将语句放在圆括号之中，就会报错。
* 因为圆括号不是运算符，所以不具有求值作用，只改变运算的优先级。

### 结合顺序

对于优先级别相同的运算符，同时出现的时候，就会有计算顺序的问题。

方式一是将左侧两个运算数结合在一起，采用这种解释方式的运算符，称为“左结合”运算符；方式二是将右侧两个运算数结合在一起，这样的运算符称为“右结合”运算符。

JavaScript 语言的大多数运算符是“左结合”，少数运算符是“右结合”，其中最主要的是赋值运算符（`=`）和三元条件运算符（`?:`）。