# 数据容器

## Set

ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。Set可以用于数组或字符串去重。

### 基本使用

向 Set 加入值的时候，不会发生类型转换。

Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（`===`），主要的区别是向 Set 加入值时认为`NaN`等于自身，而精确相等运算符认为`NaN`不等于自身。

#### 创建

`Set`函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。

将容器对象传入Set的构造函数，会创建一个Set，遍历容器对象，将元素添加到Set中后返回。

> 容器对象包括：数组、字符串、arguments、NodeList、Set

#### 属性

Set 结构的实例有以下属性。

- `Set.prototype.constructor`：构造函数，默认就是`Set`函数。
- `Set.prototype.size`：返回`Set`实例的成员总数。

#### 应用

扩展运算符（`...`）内部使用`for...of`循环，所以也可以用于 Set 结构。

##### 去重

```javascript
let unique = (arr) => [...new Set(arr)];
```

##### 集合操作

```javascript
let union = (a, b) => new Set([...a, ...b]);
let intersect = (a, b) => new Set([...a].filter(x => b.has(x)));
let difference = (a, b) => new Set([...a].filter(x => !b.has(x)));
```

### 实例方法

#### 操作方法

| 方法        | 说明                                  |
| ----------- | ------------------------------------- |
| add(val)    | 添加一个元素，返回set对象             |
| delete(val) | 删除元素，如果不存在，没有信息提示    |
| has(val)    | 返回一个布尔值，表示该值是否为set成员 |
| clear()     | 删除全部元素                          |

#### 遍历方法

| 方法      | 说明                     |
| --------- | ------------------------ |
| keys()    | 返回键名的遍历器         |
| values()  | 返回键值的遍历器         |
| entries() | 返回键值对的遍历器       |
| forEach() | 使用回调函数遍历每个成员 |

`Set`的遍历顺序就是插入顺序。

##### keys(), values(), entries()

`keys`方法、`values`方法、`entries`方法返回的都是遍历器对象。由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），所以`keys`方法和`values`方法的行为完全一致。

Set 结构的实例默认可遍历，它的默认遍历器生成函数就是它的`values`方法。

##### forEach()

`forEach`方法的参数就是一个处理函数。该函数的参数与数组的`forEach`一致，依次为键值、键名、集合本身。

`forEach`方法还可以有第二个参数，表示绑定处理函数内部的`this`对象。

## Map

JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。

ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。

### 基本使用

Map内部判断两个值是否不同，使用的算法和Set相同，叫做“Same-value-zero equality”。

#### 创建

Map 也可以接受一个数组作为参数。该数组的成员是一个个表示键值对的数组。

任何具有 Iterator 接口、且每个成员都是一个双元素的数组的数据结构都可以当作`Map`构造函数的参数。

#### 属性

`size`属性返回 Map 结构的成员总数。

### 实例方法

#### 操作方法

| 方法   | 说明                                            |
| ------ | ----------------------------------------------- |
| set    | 添加键值对元素，已有则更新，返回map本身         |
| get    | 通过键获取元素，如果键不存在，返回undefined     |
| has    | 返回布尔值，表示是否存在指定元素                |
| delete | 返回布尔值，删除键值对，如果键不存在，返回false |
| clear  | 清空map，无返回值                               |

#### 遍历方法

| 方法      | 说明                     |
| --------- | ------------------------ |
| keys()    | 返回键名的遍历器         |
| values()  | 返回键值的遍历器         |
| entries() | 返回键值对的遍历器       |
| forEach() | 使用回调函数遍历每个成员 |

表示 Map 结构的默认遍历器接口（`Symbol.iterator`属性），就是`entries`方法

## 弱引用

### WeakSet

WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别。

WeakSet 的一个用处，是储存 DOM 节点，而不用担心这些节点从文档移除时，会引发内存泄漏。

1. WeakSet 的成员只能是对象，而不能是其他类型的值
2. WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用
3. WeakSet 不可遍历

### WeakMap

`WeakMap`结构与`Map`结构类似，也是用于生成键值对的集合。

* `WeakMap`只接受对象作为键名（`null`除外），不接受其他类型的值作为键名。
* `WeakMap`的键名所指向的对象，不计入垃圾回收机制。
* `WeakMap`只有四个方法可用：`get()`、`set()`、`has()`、`delete()`。

有一个[提案](https://github.com/tc39/proposal-symbols-as-weakmap-keys)，允许 Symbol 值也可以作为 WeakMap 的键名。一旦纳入标准，就意味着键名存在两种可能：对象和 Symbol 值。

### WeakRef

WeakSet 和 WeakMap 是基于弱引用的数据结构，[ES2021](https://github.com/tc39/proposal-weakrefs) 更进一步，提供了 WeakRef 对象，用于直接创建对象的弱引用。

WeakRef 实例对象有一个`deref()`方法，如果原始对象存在，该方法返回原始对象；如果原始对象已经被垃圾回收机制清除，该方法返回`undefined`。

一旦使用`WeakRef()`创建了原始对象的弱引用，那么在本轮事件循环（event loop），原始对象肯定不会被清除，只会在后面的事件循环才会被清除。

弱引用对象的一大用处，就是作为缓存，未被清除时可以从缓存取值，一旦清除缓存就自动失效。

### FinalizationRegistry

[ES2021](https://github.com/tc39/proposal-weakrefs#finalizers) 引入了清理器注册表功能 FinalizationRegistry，用来指定目标对象被垃圾回收机制清除以后，所要执行的回调函数。

#### 创建

```javascript
const registry = new FinalizationRegistry(heldValue => {
  // ....
});
```

`FinalizationRegistry()`是系统提供的构造函数，返回一个清理器注册表实例，里面登记了所要执行的回调函数。回调函数作为`FinalizationRegistry()`的参数传入，它本身有一个参数`heldValue`。

#### 实例方法

##### register()

`register()`方法，用来注册所要观察的目标对象。

第一个参数是所要观察的目标对象，一旦该对象被垃圾回收机制清除，注册表就会在清除完成后，调用早前注册的回调函数，将第二个参数作为heldValue传入。

如果以后还想取消已经注册的回调函数，则要向`register()`传入第三个参数，作为标记值。这个标记值必须是对象，一般都用原始对象。

注册表不对目标对象、标记值构成强引用，属于弱引用。

##### unregister()

取消回调函数时，要使用`unregister()`方法，并将标记值作为该方法的参数。