# 数据类型

TypeScript中有原始类型和对象类型两类，原始类型有8种，对象类型7种。

## 原始类型

原始类型有boolean、number、string、void、null、undifined、bigint、symbol。

boolean、number、string表现形式与JavaScript中的对应类型相同。

### void

void用于没有返回值函数的返回值类型定义。一个声明为void类型的变量只能被赋值为undifined。

### null和undifined

如果tsconfig.json中`stricNullChecks`设为false，null和undifined可以赋值给任意类型。否则，null只能赋值null、any、unknown类型，undifined只能赋值给void、undifined、any、unknown类型。

### bigint

bigint能够表示任意大的整数。bigint不能使用Math对象中的方法，bigint支持 `+`、`-`、`*`、`/`、`**`、`%` 符号进行运算，也可以和Number进行比较。

bigint的定义方式：

1. 在一串数字后加n
2. BigInt函数，可以传入一串数字或数字字符串

### symbol

调用symbol函数会创建一个symbol类型的变量，每一个symbol值都是唯一，不相等。symbol函数可以传入字符串，作为对symbol的描述，可以通过`.description`获取该描述。

## 对象类型

对象类型包括object、Array、any、tuple

### object

object表示对象类型，所有对象类型都是object的子类型，都可以赋值给object类型的变量。

### Array

数组类型有两种声明方式：

1. `let arr:number[]`
2. `let arr:Array<number>`

如果数组不声明内部元素类型，则该数组可以放任意类型元素。

### any

任意类型的值都可以赋值给any类型的变量，any类型的变量表现和js中的变量相同。

### tuple

元组可以存储不同类型的元素，这些元素必须与规定的元素类型顺序对应。

* 元组可以越界添加元素，但不可以越界访问。
* 元组内元素类型后有`?`表示该元素类型对应的元素可以不存在
* 数组的剩余参数可以定义为元素类型

