# 理解对象

对象(object)是“键值对”的集合，表示属性和值的映射关系。

### 对象的属性

属性由减值对，k、v组成，k和v之间用冒号分隔，每组k :v之间用逗号分隔，最后一个k : v对后可以不书写逗号。如果对象的k不符合J5标识符命名规范，则这个键名必须用引号包裹。

* 属性的访问

  * 如果属性名符合JS标识符规范，可以用`.attr`形式访问对象中指定键的值。

  * 如果属性名不符合JS标识符命名规范，则必须用`["attr"]`方括号形式写法来访问
  * 如果通过变量形式访问属性值，则必须使用`[aVar]`方括号形式

* 属性的更改

  直接使用赋值运算符重新对某属性赋值即可更改属性

* 属性的创建

  如果对象本身没有某个属性值，则用点运算符赋值时，这个属性会被创建出来

* 属性的删除

  如果要删除某个对象的属性，需要使用delete操作符

### 对象的方法

如果某个属性值是函数，则它也被称为对象的“方法”。方法也是函数，只不过方法是对象的“函数属性”，它需要用对象通过点运算符调用

* 方法调用

  使用“点语法”可以调用对象的方法

### 对象的操作

#### 对象遍历

和遍历数组类似，对象也可以被遍历，遍历对象需要使用for. . .in.. .循环

使用for. . .in...循环可以遍历对象的每个键

#### 对象的克隆

对象是引用类型值，这意味着:

1. 不能用var obj1= obj1这样的语法克隆一个对象
2. 使用`==`或者`===`进行对象的比较时，比较的是它们是否为内存中的同一个对象，而不是比较值是否相同

**浅克隆**

浅克隆:只克隆对象的“表层”，如果对象的某些属性值又是引用类型值，则不进一步克隆它们，只是传递它们的引用。

可以通过调用Object.assign方法实现对象的浅克隆。

```javascript
var ob1 = {
    a:1,
    b:2
}
var obj2 = Object.assign({},obj1);
```

**深克隆**

克隆对象的全部，即引用类型值也进行克隆。

```javascript
function deepClone(value) {
    var result;
    if(Array.isArray(value)){
        result = value.map(deepClone);
    }else if(typeof value ==="object"){
        result = {};
        for(var key in value){
            result[key] = deepClone(value[key]);
        }
    }else{
        result = value;
    }
    return result;
}
function deepCopy(value) {
    if (Array.isArray(value))return copyElementFor(value,[]);
    else if(typeof value==="object")return copyElementFor(value,{});
    else return value;

    function copyElementFor(value,collection) {
        for(var attr in value){
           collection[attr] = deepCopy(value[attr]);
        }
        return collection;
    }
}
```



