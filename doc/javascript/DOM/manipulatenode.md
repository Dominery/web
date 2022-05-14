# 节点操作

## 访问元素节点

得到页面上的元素节点是对节点进行操作的前提。访问元素节点主要依靠document对象

#### document

document对象是DOM中最重要的东西，几乎所有DOM的功能都封装在了document对象中。

document对象也表示整个HTML文档，它是DOM节点树的根；document.body可以获取body元素；document.documentElement可以获取html元素，document.firstChild、document.childNodes[0]实现相同的效果。

document对象的nodeType属性值是9

#### 访问元素节点常用方法

| 方法                              | 说明                   | 兼容性 |
| --------------------------------- | ---------------------- | ------ |
| document.getElementById()         | 通过id得到元素         | IE6    |
| document.getElementsByTagName()   | 通过标签名得到元素数组 | IE6    |
| document.getElementsByclassName() | 通过类名得到元素数组   | IE9    |
| document.querySelector()          | 通过选择器得到元素     | IE8+   |
| document.querySelectorAll()       | 通过选择器得到元素数组 | IE8+   |

* 节点元素

  任何一个节点元素也可以调用getElementsByTagName()方法，从而得到其内部的某种类的元素节点
  
* 动态获取

  getElementsByClassName()方法和getElementsByTagName()方法可以动态获取元素，可以理解为:页面上增加或者删除元素时，获取的元素个数可以改变，而iquerySelectorAll()方法做不到此效果。

#### 元素节点的关系

![](../../../images/domNodeRel.jpg)

在标准的W3C规范中，空白文本节点也应该算作节点，但是在lE8及以前的浏览器中会有一定的兼容问题，它们不把空文本节点当做节点。从IE9开始支持一些“只考虑元素节点”的属性，从而排除文本节点的干扰。

| 关系           | 所有节点        | 元素节点               |
| -------------- | --------------- | ---------------------- |
| 子节点         | childNodes      | children               |
| 父节点         | parentNode      | 同                     |
| 第一个子节点   | firstChild      | firstElementChild      |
| 最后一个子节点 | lastChild       | lastElementChild       |
| 前一个兄弟节点 | previousSibling | previousElementSibling |
| 后一个兄弟节点 | nextSibling     | nextElementSibling     |

## 节点修改

### 创建节点

#### 创建

document.createElement()方法用于创建一个指定tagname的HTML元素节点

**克隆节点**

cloneNode()方法可以克隆节点，可以填写布尔值参数，表示是否采用深度克隆:如果为true，则该节点的所有后代节点也都会被克隆，如果为false，则只克隆该节点本身

#### 挂载

新创建的节点没有被加载到DOM中，无法被看见，必须继续使用appendChild()或insertBefore()方法将其插入到DOM树上

* appendChild()

  已经在DOM树上的节点，都可以调用appendChild()方法，它可以将节点挂载到它的内部，成为它的最后一个子节点

* insetBefore()

  已经在DOM树上的节点，都可以调用insertBefore()方法，它可以将节点挂载到它的内部，成为它的参考子节点之前的节点

### 更新节点

#### 元素节点位置

**offetTop**

DOM元素都有offsetTop属性，表示此元素到定位祖先元素的垂直距离

> 定位祖先元素:在祖先中，离自己最近的且拥有定位属性的元素

#### 元素节点内容

改变元素节点中的内容可以使用两个相关属性:innerHTML和 innerText

innerHTML属性能以HTML语法设置节点中的内容

innerText属性只能以纯文本的形式设置节点中的内容，获取时会忽略标签和空格

#### 元素节点的HTML属性

元素属性不区分大小写。

* 标准W3C属性

  * 元素节点样式

    1. 调用元素节点的style设置其样式属性的属性值，这种方式是设置了元素的行内样式
  2. 改变元素节点的className属性，使已有样式能够作用到元素上。
    3. 通过元素节点提供的classList对象方便添加和移除属性值，IE10兼容
  
  * 元素属性

    如src、href等等，只需要直接打点进行更改即可

  * 表单元素属性

    获取到type、value、disabled等属性

* 不符合W3C标准的属性

  1. 元素属性可以使用setAttribute()、getAttribute()、removeAttribute()来设置、读取、移除

  2. 如果属性符合`data-<attrName>`的格式，可以使用dataset.`<attrName>`设置、读取，IE11兼容

### 操纵节点

操纵节点的方法都需要在父节点上进行调用。

* 移动

  如果将已经挂载到DOM树上的节点成为appendchild()或insertBefore()的参数，这个节点将会被移动。

  insertBefore()接收两个参数：要插入的节点和参照节点。如果参照节点为null，效果与appendChild()相同。

* 替换

  replaceChild()接收两个参数，要插入的节点和要替换的节点，返回被替换的节点

* 移除

  removeChild()接收要移除的节点作为参数并返回。

## HTML5规范

HTML5规范规定了一些浏览器需要提高的JavaScript API。

### CSS类拓展

#### getElementsByClassName()

getElementsByClassName()方法接收一个参数，即包含一个或多个类名的字符串，返回类名中包含相应类的元素的NodeList。

#### classList

DOM节点的className是个字符串，操作类名不方便。classList是一个新的集合类型，提供操作类名的相关方法。

* add()，向类名列表中添加指定的字符串
* contains()，表示给定的value是否存在
* remove()，从类名列表中删除指定的字符串
* toggle()，如果类名列表中已经存在指定的value，则删除；如果不存在，则添加

### 焦点管理

document.activeElement，始终包含当前拥有焦点的DOM元素。document.hasFocus()方法，该方法返回布尔值，表示文档是否拥有焦点。

### HTMLDocument扩展

#### readyState属性

document.readyState属性有两个可能的值

*  loading，表示文档正在加载
* complete，表示文档加载完成

#### compatMode属性

指示浏览器当前处于什么渲染模式，标准模式下document.compatMode的值是"CSS1Compat"，而在混杂模式下，document.compatMode的值是"BackCompat"。

#### head属性

document.head属性能够获取`<head>`元素。

### 自定义数据属性

HTML5允许给元素指定非标准的属性，但要使用前缀data-以便告诉浏览器，这些属性既不包含与渲染有关的信息，也不包含元素的语义信息，元素的dataset属性能够访问自定义属性。

### 插入标记

使用innerHTML、outerHTML对元素进行替换，如果被替换的元素存在事件绑定或JavaScript引用，那么被绑定关系不会被删除，这会导致内存泄漏。

#### innerHTML属性

在读取innerHTML属性时，会返回元素所有后代的HTML字符串，包括元素、注释和文本节点。而在写入innerHTML时，则会根据提供的字符串值以新的DOM子树替代元素中原来包含的所有节点。

#### outerHTML属性

读取outerHTML属性时，会返回调用它的元素（及所有后代元素）的HTML字符串。在写入outerHTML属性时，调用它的元素会被传入的HTML字符串经解释之后生成的DOM子树取代。

#### insertAdjacentHTML()与insertAdjacentText()

接收两个参数：要插入标记的位置和要插入的HTML或文本。第一个参数必须是下列值中的一个：

* beforebegin，插入当前元素前面，作为前一个同胞节点；
* afterbegin，插入当前元素内部，作为新的子节点或放在第一个子节点前面；
* beforeend，插入当前元素内部，作为新的子节点或放在最后一个子节点后面
* afterend，插入当前元素后面，作为下一个同胞节点

### scrollIntoView()

scrollIntoView()方法存在于所有HTML元素上，可以滚动浏览器窗口或容器元素以便包含元素进入视口。该方法接收alignToTop和scrollIntoViewOption作为参数。

alignToTop是一个布尔值。

* true：窗口滚动后元素的顶部与视口顶部对齐
* false：窗口滚动后元素的底部与视口底部对齐

scrollIntoViewOptions是一个选项对象。

* behavior：定义过渡动画，可取的值为"smooth"和"auto"，默认为"auto"
* block：定义垂直方向的对齐，可取的值为"start"、"center"、"end"和"nearest"，默认为"start"
* inline：定义水平方向的对齐，可取的值为"start"、"center"、"end"和"nearest"，默认为"nearest"