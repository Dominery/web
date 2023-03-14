# Typescript





### type和interface

**对象和函数**

type和interface都可以表示对象结构和函数类型，只是语法不同。

**其他类型**

interface对于除对象、函数外的其他类型无法表示，type可以表示，如原始类型、联合类型、元组。

**继承**

interface和type都可以各自继承，也可以互相继承，只是语法不同。

**实现**

类可以实现interface和type，但是如果type是联合类型会报错。

**拓展**

interface可以被定义多次，定义的类型信息会进行合并。

**类型计算**

type可以通过`keyof`、`typeof`对类型进行计算，同时结合泛型可以对类型属性进行拓展。