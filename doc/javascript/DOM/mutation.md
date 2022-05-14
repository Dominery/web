# MutationObserver接口

使用MutationObserver可以观察整个文档、DOM树的一部分，或某个元素，当DOM被修改时异步执行回调。

## 基本用法

向MutationObserver构造函数传入一个回调函数创建一个MutationObserver(下文简称MO)实例。当MO监听的DOM属性发生修改时，会调用回调函数，传入一个MutationRecord对象的数组和MO实例。

**observe()**

MO实例需要通过observe()方法才能监听DOM修改，observe方法接收两个参数：DOM节点，MutationObserverInit(下称MOI)对象。MOI对象是一个键值对，属性名表示监听的属性，属性值为布尔类型，表示是否监听。

多次调用observe()方法，能够实现监听多个节点的修改，可以通过MR对象的target属性进行分辨。

**disconnect()**

MO实例调用disconnect()方法会终止监听，如果是同步调用该方法，已经发生的改变不会进入回调。

## MOI对象

MOI对象控制监听的范围，可以监听的事件有属性变化、文本变化和子节点变化。

| 属性                  | 说明                           |
| --------------------- | ------------------------------ |
| subtree               | bool值，监听子节点             |
| attributes            | bool值，监听节点的属性变化     |
| attributeFilter       | 数组，只监听节点在数组中的属性 |
| attributeOldValue     | bool，记录以前的值             |
| characterData         | bool，修改字符数据             |
| characterDataOldValue | bool，记录修改前的字符数据     |
| childList             | bool，监听子节点是否变化       |

