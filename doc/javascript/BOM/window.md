# Window对象

window对象是当前JS脚本运行所处的窗口，而这个窗口中包含DOM结构，window.document属性就是document对象。在浏览器中，window对象有双重角色，它既是通过JavaScript访问浏览器窗口的一个接口，又是ECMAScript规定的Global对象。

在有标签页功能的浏览器中，每个标签都拥有自己的window对象;也就是说，同一个窗口的标签页之间不会共享一个window对象。

### 全局作用域

由于window对象同时扮演着ECMAScript中Global对象的角色，因此所有在全局作用域中声明的变量、函数都会变成window对象的属性和方法。

#### 全局变量

全局变量会成为window对象的属性。这就意味着，多个js文件之间是共享全局作用域的，即js文件没有作用域隔离功能。

**window属性与全局变量区别**

* 全局变量不能通过delete操作符删除，而直接在window对象上的定义的属性可以。

    > IE8及更早版本在遇到使用delete删除window属性的语句时，不管该属性最初是如何创建的，都会抛出错误，以示警告。IE9及更高版本不会抛出错误。

* 尝试访问未声明的变量会抛出错误，但是通过查询window对象，可以知道某个可能未声明的变量是否存在。

> Windows Mobile平台的IE浏览器不允许通过window.property=value之类的形式，直接在window对象上创建新的属性或方法。

#### 内置函数

如setlnterval()、alert()等内置函数，普遍是window的方法

### 窗口尺寸相关属性

| 属性        | 说明                                     |
| ----------- | ---------------------------------------- |
| innerHeight | 浏览器窗口的内容区域高度，包含水平滚动条 |
| innerWidth  | 浏览器窗口的内容区域宽度，包含垂直滚动条 |
| outerHeight | 浏览器窗口的外部高度                     |
| outerWidth  | 浏览器窗口的外部宽度                     |

获得不包含滚动条的窗口宽度，要用document.documentElement.clientWidth

#### 已卷动高度

window.scrollY属性表示在垂直方向已滚动的像素值。

document.documentElement.scrollTop属性也表示窗口卷动高度。

document.documentElement.scrollTop不是只读的，而window.scrollY是只读的

```javascript
var scroollTop = window.scrollY || document.documentElement.scrollTop;
```



### 事件

#### resize事件

在窗口大小改变之后，就会触发resize事件，可以使用window.onresize或者window.addEventListener('resize')来绑定事件处理函数

#### scroll事件

在窗口被卷动之后，就会触发scroll事件，可以使用window.onscroll或者window.addEventListener('scroll')来绑定事件处理函数