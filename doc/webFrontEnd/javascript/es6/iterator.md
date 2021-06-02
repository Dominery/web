# 迭代器

Iterator，遍历器，用于遍历容器中元素。

### iterator的使用

数组可以使用方括号语法访问Symbol.iterator方法，通过调用该方法，将获得一个iterator可迭代对象，iterator可以用next方法获取一个对象，由遍历的下一个元素和遍历结束标志构成。

```javascript
const it = [1,2][Symnbol.iterator]();
it.next();//{value:1,done:false}
it.next();//{value:2,done:false}
it.next();//{value:undefied,done:true}

let next;
const ite = [1,2][Symbol.iterator]();
while(!(next=ite.next).done){
    console.log(next.value);
}
```

> Symbol是ES6中引入的一种新的基本数据类型，用于表示一个独一无二的值。它是JavaScript 中的第七种数据类型。

为了方便iterator使用，es6提供for...of语法糖。

### for...of

```javascript
for(const item of arr){
    
}
```

for...of可以和continue和break结合使用。

#### 取得数组的索引

通过调用数组的keys()方法可以得到索引的可迭代对象，对其进行遍历。

es6提供了value()方法用来获取值的可迭代对象。

entries()方法可以得到由索引和值组成的数组的可迭代对象。

```javascript
for(const key of arr.keys()){
    
}
for(const [key,value] of arr.entries()){
    
}
```

### 可遍历

只要对象有Symbol.iterator方法，并且这个方法可以生成可遍历对象，该对象就是可遍历的。从而可以通过for...of循环来统一遍历过程。

* 原生可遍历对象

  数组、字符串、Set、Map、arguments、NodeList

* 非原生可遍历

  普通的对象

### 应用

1. 数组展开运算符
2. 数组的解构赋值
3. Set、Map构造函数