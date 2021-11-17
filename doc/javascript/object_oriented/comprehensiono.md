# 理解对象

对象(object)是“键值对”的集合，表示属性和值的映射关系。

## 对象的构成

### 对象的属性

属性由减值对，k、v组成，k和v之间用冒号分隔，每组k :v之间用逗号分隔，最后一个k : v对后可以不书写逗号。如果对象的k不符合J5标识符命名规范，则这个键名必须用引号包裹。

* 属性的访问

  * 点语法

    如果属性名符合JS标识符规范，可以用`.attr`形式访问对象中指定键的值。

  * 方括号语法

    如果属性名不符合JS标识符命名规范，则必须用`["attr"]`方括号形式写法来访问

    如果通过变量形式访问属性值，则必须使用`[aVar]`方括号形式

    方括号内可以放任何能够通过计算得到值的表达式。

    > es6增强了方括号形式，可以将方括号放在字面量中。

* 属性的创建和更改

  如果对象本身没有某个属性值，则属性访问方法对其赋值，这个属性会被创建出来。

  如果该属性已经存在，赋值会更新属性值。

* 属性的删除

  如果要删除某个对象的属性，需要使用delete操作符
  
* 属性的简洁表示法

  es6新增创建对象字面量时将变量作为属性值的特性。

  ```javascript
  const age = 23;
  const person = {age};
  ```

  键名和变量或常量名一样的时候，可以只写一个

### 对象的方法

如果某个属性值是函数，则它也被称为对象的“方法”。方法也是函数，只不过方法是对象的“函数属性”，它需要用对象通过点运算符调用

* 方法调用

  使用“点语法”可以调用对象的方法
  
* 方法简洁表示法

  es6可以省略方法冒号和function关键字。

#### 方法重载

如果两个方法的名称相同，但形参个数不同，那么称这种现象为方法重载。

JavaScript中没有支持方法重载的语法机制，然而我们通过应用闭包和arguments可以实现方法重载的功能，实现代码如下。

```javascript
function addMethod(obj,name,func) {
    let oldFunc = obj[name];
    obj[name] = function(){
        if(func.length===arguments.length){
            func.apply(this,arguments);
        }else if(typeof oldFunc ==="function"){
            oldFunc.apply(this,arguments);
        }
    }
}
```

> addMethod函数传入的三个参数分别为需要实现方法重载功能的对象、方法名、匿名函数。注意，该匿名函数必须是通过function定义。
>
> 函数体内使用oldFunc保存之前的同名方法。在方法调用时，如果形参个数与实参个数不匹配，则会通过oldFunc逐次调用之前的方法。

如果想要在类中实现方法重载，需要在类的原型上添加方法。

## 对象的操作

### 对象遍历

和遍历数组类似，对象也可以被遍历，遍历对象需要使用for. . .in.. .循环

1. 使用for. . .in...循环可以遍历对象的每个键。
2. 使用Objec.keys()返回由对象键构成的数组，对该数组遍历

### 对象的克隆

对象是引用类型值，这意味着:

1. 不能用var obj1= obj1这样的语法克隆一个对象
2. 使用`==`或者`===`进行对象的比较时，比较的是它们是否为内存中的同一个对象，而不是比较值是否相同

**浅拷贝**

浅克隆:只克隆对象的“表层”，如果对象的某些属性值又是引用类型值，则不进一步克隆它们，只是传递它们的引用。

可以通过调用Object.assign方法实现对象的浅克隆。

```javascript
var ob1 = {
    a:1,
    b:2
}
var obj2 = Object.assign({},obj1);
```

**深拷贝**

克隆对象的全部，即引用类型值也进行克隆。

```javascript
function deepClone(value) {
    if(typeof value !== 'object' || value == null)	{
        return value
    }
    let result;
    if(Array.isArray(value)){
        result = value.map(deepClone);
    }else{
        result = Object.entries(value).reduce((result,[key,value])=>{
            result[key] = deepClone(value);
            return result;
        },{})
    }
    return result;
}
function deepCopy(value) {
    if(typeof value !== 'object' || value == null) return value;
    else if(Array.isArray(value))return copyElementFor(value,[]);
    else return copyElementFor(value,{});

    function copyElementFor(value,collection) {
        for(var attr in value){
           if(!value.hasOwnProperty(attr)){
               continue;
           }
           collection[attr] = deepCopy(value[attr]);
        }
        return collection;
    }
}
```

### 对象展开

通过展开运算符可以将对象属性存放到空对象中，构成新对象。

可以通过对象展开合并多个对象的属性。es6新增Object.assign()方法用来合并对象，但是不构成新对象，将其余对象属性添加到第一个对象中。

* 如果展开空对象，没有任何效果
* 如果展开的不是对象，则会自动将其转为对象，再将其属性罗列出来
* 如果展开运算符后面是字符串，它会自动转成一个类似数组的对象，因此返回的不是空对象

**应用**

* 克隆对象
* 用户参数和默认参数