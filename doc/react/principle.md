# React原理

## 函数式编程

* 纯函数
* 不可变值

## vdom和diff

框架给你的保证是，你在不需要手动优化的情况下，我依然可以给你提供过得去的性能。Virtual DOM的优势不在于单次的操作，而是在大量、频繁的数据更新下，能够对视图进行合理、高效的更新。

虚拟dom是一种架构方案，目的是为提高项目代码的可维护性，最大的优势在于抽象了原本的渲染过程，实现了跨平台的能力，而不是提升性能。

diff算法是找到新旧虚拟dom转化的最小操作方式，但这总共消耗的时间可能依旧不如操作原生DOM少。

树的diff算法时间复杂度O(n^3^)，vdom的diff算法优化到O(n)。

* 只比较同一层级，不跨级比较
* 如果旧虚拟DOM找到了新虚拟DOM相同的key
  * 若虚拟DOM内容没变，直接使用之前的真实DOM
  * 若虚拟DOM内容变了，生成新的真实DOM，随后替换页面的真实DOM
* 如果旧虚拟DOM中未找到新虚拟DOM相同的key，创建新的真实DOM，随后渲染

#### 使用index作为key可能引发的问题

1. 若对数据进行逆序添加、逆序删除操作会产生没有必要的真实DOM更新
2. 如果结构中包含输入类DOM，会产生错误DOM更新
3. 如果不存在破坏顺序操作，没有问题

## JSX本质

JSX是编译语法，本质是通过React.createElement创建虚拟节点，之后再通过patch函数将虚拟节点渲染到网页上。

> React.createElement第一个参数是组件或者代表tag的字符串，第二个参数接收一个对象，对象保存节点的属性，后面的参数代表子节点，也可以使用数组作为第三个参数接收。

## 合成事件

React使用SyntheticEvent作为合成事件，主要有以下原因：

* 兼容性、跨平台
* 事件挂载到root对象(React17前是document)，减少内存消耗，避免频繁解绑
* 方便事件统一管理

## State

### setState

在18之前，对于setState，react会采用批量更新的方式更新state，通过一个开关来实现批量更新。所以处于微任务或者红任务的setState无法被合并。

在18之后，对于setState，react批量更新的方式不再是通过开关，而是通过事件的优先级，同一优先级的事件会被统一处理，处于微任务和红任务之间的setState也会被合并。

### setState、useState异同

首先从原理角度出发，setState和 useState 更新视图，底层都调用了scheduleUpdateOnFiber 方法，而且事件驱动情况下都有批量更新规则。

在不是 pureComponent 组件模式下， setState 不会浅比较两次 state 的值，只要调用 setState，在没有其他优化手段的前提下，就会执行更新。

但是useState的setState，在创建第一个update的时候，会先执行得到新的state，并且进行比较，若值相同，不会开启新的一轮调度

setState 有专门监听 state 变化的回调函数 callback，可以获取最新state；但是在函数组件中，只能通过 useEffect 来执行 state 变化引起的副作用。

## 组件渲染过程

1. props state
2. render()生成虚拟节点
3. 挂载虚拟节点

更新

1. setState将组件保存在dirtyComponents中
2. render()生成虚拟节点
3. 挂载虚拟节点

## React-Router原理

React-Router有两种实现方式，hash和browser。

* 基于hash的路由通过监听hashchange事件感知到页面发生变化
* 基于browser的路由通过history提供的api(pushState、replaceState)改变url