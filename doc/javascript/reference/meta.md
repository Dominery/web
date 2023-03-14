# 元编程

## Proxy

Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改。

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。

### 创建

#### Proxy()

ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。

```javascript
var proxy = new Proxy(target, handler);
```

`new Proxy()`表示生成一个`Proxy`实例，`target`参数表示所要拦截的目标对象，`handler`参数也是一个对象，用来定制拦截行为。

handler对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作。

* 如果`handler`没有设置任何拦截，那就等同于直接通向原对象。
* Proxy 实例也可以作为其他对象的原型对象。
* 对于可以设置、但没有设置拦截的操作，则直接落在目标对象上，按照原先的方式产生结果。

#### Proxy.revocable()

`Proxy.revocable()`方法返回一个可取消的 Proxy 实例。

`Proxy.revocable()`方法返回一个对象，该对象的`proxy`属性是`Proxy`实例，`revoke`属性是一个函数，可以取消`Proxy`实例。当执行`revoke`函数之后，再访问`Proxy`实例，就会抛出一个错误。

### 拦截操作

 Proxy 支持的拦截操作一览，一共 13 种。

- **get(target, propKey, receiver)**：拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。
- **set(target, propKey, value, receiver)**：拦截对象属性的设置，比如`proxy.foo = v`或`proxy['foo'] = v`，返回一个布尔值。
- **has(target, propKey)**：拦截`propKey in proxy`的操作，返回一个布尔值。
  - `has()`拦截只对`in`运算符生效，对`for...in`循环不生效
  - 如果某个属性不可配置（或者目标对象不可扩展），则`has()`方法就不得“隐藏”
- **deleteProperty(target, propKey)**：拦截`delete proxy[propKey]`的操作，返回一个布尔值。
  - 如果这个方法抛出错误或者返回`false`，当前属性就无法被`delete`命令删除。
- **ownKeys(target)**：拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in`循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性。
  - `ownKeys()`方法返回的数组成员，只能是字符串或 Symbol 值。如果有其他类型的值，或者返回的根本不是数组，就会报错。
  - 如果目标对象自身包含不可配置的属性，则该属性必须被`ownKeys()`方法返回，否则报错。
  - 如果目标对象是不可扩展的（non-extensible），这时`ownKeys()`方法返回的数组之中，必须包含原对象的所有属性，且不能包含多余的属性，否则报错。
- **getOwnPropertyDescriptor(target, propKey)**：拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。
- **defineProperty(target, propKey, propDesc)**：拦截`Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。
- **preventExtensions(target)**：拦截`Object.preventExtensions(proxy)`，返回一个布尔值。
  - 只有目标对象不可扩展时（即`Object.isExtensible(proxy)`为`false`），`proxy.preventExtensions`才能返回`true`，否则会报错。
- **getPrototypeOf(target)**：拦截`Object.getPrototypeOf(proxy)`，返回一个对象。
- **isExtensible(target)**：拦截`Object.isExtensible(proxy)`，返回一个布尔值。
  - 它的返回值必须与目标对象的`isExtensible`属性保持一致，否则就会抛出错误。
- **setPrototypeOf(target, proto)**：拦截`Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
- **apply(target, object, args)**：拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。接受三个参数，分别是目标对象、目标对象的上下文对象（`this`）和目标对象的参数数组。
- **construct(target, args)**：拦截 Proxy 实例作为构造函数调用的操作，比如`new proxy(...args)`。
  - `construct()`方法返回的必须是一个对象，否则会报错。
  - 目标对象必须是函数，否则就会报错。
  - `construct()`方法中的`this`指向的是`handler`，而不是实例对象。

如果一个属性不可配置（configurable）且不可写（writable），则 Proxy 不能修改该属性，否则通过 Proxy 对象访问该属性会报错。

#### this问题

在 Proxy 代理的情况下，目标对象内部的`this`关键字会指向 Proxy 代理。

`this`绑定原始对象，就可以解决这个问题。

Proxy 拦截函数内部的`this`，指向的是`handler`对象。

## Reflect

`Reflect`对象是 ES6 为了操作对象而提供的新 API。

### 设计目的

1. 将`Object`对象的一些明显属于语言内部的方法
2. 修改某些`Object`方法的返回结果，让其变得更合理
3. 让`Object`操作都变成函数行为
4. `Reflect`对象的方法与`Proxy`对象的方法一一对应

### 静态方法

| 方法                                                    | 说明                                                         |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| Reflect.get(target, name, receiver)                     | 查找并返回`target`对象的`name`属性，如果没有该属性，则返回`undefined`。如果`name`属性部署了读取函数（getter），则读取函数的`this`绑定`receiver`。 |
| Reflect.set(target, name, value, receiver)              | 设置`target`对象的`name`属性等于`value`。如果`name`属性设置了赋值函数，则赋值函数的`this`绑定`receiver`，之后会触发Proxy.defineProperty。 |
| Reflect.has(obj, name)                                  | 对应`name in obj`里面的`in`运算符。                          |
| Reflect.deleteProperty(obj, name)                       | 等同于`delete obj[name]`，用于删除对象的属性。如果删除成功，或者被删除的属性不存在，返回`true`；删除失败，被删除的属性依然存在，返回`false`。 |
| Reflect.construct(target, args)                         | 等同于`new target(...args)`，这提供了一种不使用`new`，来调用构造函数的方法。 |
| Reflect.getPrototypeOf(obj)                             | 用于读取对象的`__proto__`属性。如果参数不是对象，`Object.getPrototypeOf`会将这个参数转为对象，然后再运行，而`Reflect.getPrototypeOf`会报错。 |
| Reflect.setPrototypeOf(obj, newProto)                   | 设置目标对象的原型（prototype），返回一个布尔值，表示是否设置成功。 |
| Reflect.apply(func, thisArg, args)                      | 用于绑定`this`对象后执行给定函数。                           |
| Reflect.defineProperty(target, propertyKey, attributes) | 用来为对象定义属性。                                         |
| Reflect.getOwnPropertyDescriptor(target, propertyKey)   | 用于得到指定属性的描述对象                                   |
| Reflect.isExtensible (target)                           | 返回一个布尔值，表示当前对象是否可扩展。                     |
| Reflect.preventExtensions(target)                       | 用于让一个对象变为不可扩展。它返回一个布尔值，表示是否操作成功。 |
| Reflect.ownKeys (target)                                | 等同于`Object.getOwnPropertyNames`与`Object.getOwnPropertySymbols`之和。 |

##### Reflect.setPrototypeOf(obj, newProto)

如果第一个参数不是对象，`Object.setPrototypeOf`会返回第一个参数本身，而`Reflect.setPrototypeOf`会报错。

如果第一个参数是`undefined`或`null`，`Object.setPrototypeOf`和`Reflect.setPrototypeOf`都会报错。

##### Reflect.isExtensible (target)

如果参数不是对象，`Object.isExtensible`会返回`false`，因为非对象本来就是不可扩展的，而`Reflect.isExtensible`会报错。

##### Reflect.preventExtensions(target)

如果参数不是对象，`Object.preventExtensions`在 ES5 环境报错，在 ES6 环境返回传入的参数，而`Reflect.preventExtensions`会报错。