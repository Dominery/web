# DOM事件

DOM允许JavaScript代码让HTML元素对事件作出反应，而这需要设置事件监听，将事件处理函数绑定在特定事件上，事件处理函数需要使用事件对象的相关属性获知到事件信息从而作出反应。

## 设置事件监听

DOM中设置事件监听的方法主要有onxxx和addEventListener()两种。

### DOM0级事件监听

最简单的给元素设置事件监听的方法就是设置它们的onxxx属性。

```javascript
onBox.onclick = ()=>{};
```

### DOM2级事件监听

通过addEventListenler()方法设置事件处理函数。

```javascript
oBox.addEventListener("click",()=>{
	//事件处理函数
},true);
```

第三个参数设置监听的阶段，true监听捕获阶段，false监听冒泡阶段。

## 事件类型

#### 鼠标事件

| 事件        | 说明                                     |
| ----------- | ---------------------------------------- |
| click       | 当鼠标单击某个对象                       |
| dblclick    | 当鼠标双击某个对象                       |
| mousedown   | 当某个鼠标按键在某个对象上被按下         |
| mouseup     | 当某个鼠标按键在某个对象上被松开         |
| mousemove   | 当某个鼠标按键在某个对象上被移动         |
| mouseenter  | 当鼠标进入某个对象（相似事件onmouseover) |
| mouseleave  | 当鼠标离开某个对象（相似事件onmouseout)  |
| contextmenu | 当鼠标点击右键上下文菜单显示             |
| selectstart | 当鼠标选中文字                           |

muoseenter不冒泡，mouseover冒泡。

click事件触发的前提是mousedown事件触发后，紧接着又在同一个元素上触发了mouseup事件。dblclick事件触发前提是连续的两次click事件。

#### 键盘事件

| 事件      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| keypress  | 当某个键盘的键被按下(系统按钮如箭头键和功能键无法得到识别)   |
| keydown   | 当某个键盘的键被按下(系统按钮可以识别，并且会先于onkeypress发生) |
| keyup     | 当某个键盘的键被松开                                         |
| textInput | 在字符被输入到可编辑区域时触发                               |

keyCode 属性返回某个事件触发的键的值的字符代码

textInput只在可编辑区域上触发，在event对象上提供了一个data属性，包含要插入的字符

#### 表单事件

| 事件   | 说明                                   |
| ------ | -------------------------------------- |
| change | 当用户已经改变域的内容                 |
| input  | 当用户正在改变域的内容                 |
| focus  | 当某元素获得焦点（比如tab键或鼠标点击) |
| blur   | 当某元素失去焦点                       |
| submit | 当表单被提交                           |
| reset  | 当表单被重置                           |

#### 页面事件

| 事件             | 说明                                                     |
| ---------------- | -------------------------------------------------------- |
| load             | 当页面或图像被加载完成                                   |
| unload           | 当用户退出页面                                           |
| DOMContentLoaded | html文档加载完成，不需要等待样式表、图像、子框架页面加载 |

## 事件传播

事件的传播是:先从外到内，然后再从内到外，分别称为捕获阶段、冒泡阶段。没有冒泡的事件：onblur、onfocus、onmouseenter、onmouseleave。

最内部元素不再区分捕获和冒泡阶段，会先执行代码写在前面的监听，然后执行后写的监听。

如果给元素设置相同的两个或多个同名事件，则DOM0级写法后面写的会覆盖先写的;而DOM2级会按顺序执行

onxxx这样的写法只能监听冒泡阶段，addEventListenler()方法可以监听捕获阶段。

#### 事件委托

如果批量添加事件监听或者动态绑定，每一个事件监听注册都会消耗一定的系统内存，而添加事件太多会导致监听数量太多，处理函数过多，内存消耗会非常大。事件委托用于解决这个问题。

事件委托利用事件冒泡机制，将后代元素事件委托给祖先元素。如果监听的事件不冒泡，则不能使用事件委托，例如onmouseenter。

**属性**

事件委托需要借助target属性获取到触发事件的后代元素。

| 属性          | 说明                                 |
| ------------- | ------------------------------------ |
| target        | 触发此事件的最早元素，即“事件源元素” |
| currentTarget | 事件处理程序附加到的元素             |

**适用场景**

* 当有大量类似元素需要批量添加事件监听时，使用事件委托可以减少内存开销
* 当有动态元素节点上树时，使用事件委托可以让新上树的元素具有事件监听
* 如果最内层元素有额外的内层元素，不应该使用事件委托

## 事件对象

事件处理函数提供一个形式参数，它是一个对象，封装了本次事件的细节，这个参数通常用单词event表示。

#### 鼠标事件对象(MouseEvent)

| 属性    | 说明                               |
| ------- | ---------------------------------- |
| clientX | 鼠标指针相对于浏览器的水平坐标     |
| clientY | 鼠标指针相对于浏览器的垂直坐标     |
| pageX   | 鼠标指针相对于整张网页的垂直坐标   |
| pageY   | 鼠标指针相对于整张网页的垂直坐标   |
| offsetX | 鼠标指针相对于事件源元素的水平坐标 |
| offsetY | 鼠标指针相对于事件源元素的垂直坐标 |

DOM规定offsetX、offestY计算的是到最内层元素的坐标

**修饰键**

DOM规定了4个属性来表示修饰键的状态：shiftKey、ctrlKey、altKey和metaKey。这几属性会在各自对应的修饰键被按下时包含布尔值true，没有被按下时包含false。

**鼠标按键**

对mousedown和mouseup事件来说，event对象上会有一个button属性，表示按下或释放的是哪个按键。DOM为这个button属性定义了3个值：0表示鼠标主键、1表示鼠标中键（通常也是滚轮键）、2表示鼠标副键。

**鼠标滚轮事件**

鼠标滚轮事件在不同浏览器中事件对象提供的方法不同，事件监听不同。

* Chrome及IE

  通过onmousewheel属性设置事件监听。

  事件对象e提供deltaY，wheelDelta属性表示鼠标滚动方向。deltaY为向上-125、向下125，wheelDelta为向上150、向下-150。

* FireFox

  通过2级事件监听方法设置`DOMMouseScroll`事件监听。

  事件对象e提供detail属性用以获取滚动信息，向下滚动时返回正值，向上滚动时返回负值，取值在-3-3之间。

**示例代码**

```javascript
function addWindowMousewheel(handler) {
    if(window.addEventListener)
        window.addEventListener("DOMMouseScroll",wheel,false);
    window.onmousewheel = wheel;

    function wheel(event) {
        var delta = 0;
        if(!event)event = window.event;
        if(event.deltaY){
            delta = event.deltaY/125;
        }else if(event.detail){ //FireFox浏览器使用detail,值为-3~3
            delta = event.detail/3;
        }
        if(delta)handler(delta);
    }
}
```

#### 键盘事件

e.charCode属性通常用于onkeypress事件中，表示用户输入的字符的“字符码”

e.keyCode属性通常用于onkeydown事件和onkeyup中，表示用户按下的按键的“键码”

**charCode字符码**

| 字符 | 字符码 |
| ---- | ------ |
| 0~9  | 48~57  |
| A~Z  | 65~90  |
| a~z  | 97~122 |

**keyCode建码**

| 按键              | 建码           |
| ----------------- | -------------- |
| 0~9               | 48~57          |
| a~z(不区分大小写) | 65~90          |
| ←↑→↓              | 37、38、39、40 |
| 回车              | 13             |
| 空格              | 32             |

#### 改变原有事件

**阻止默认事件**

* e .preventDefault()
* 在事件处理函数中返回false
* `e.returnValue`低版本ie浏览器

**阻止冒泡**

* e.stopPropagation()方法
* e.cancelBubble = true低版本ie浏览器

## 模拟事件

document.createEvent()方法能够创建一个event对象。这个方法接收一个参数，此参数是一个表示要创建事件类型的字符串。

DOM对象上的dispatchEvent()方法能够触发事件，该方法接收一个参数，即表示要触发事件的event对象。调用方法后，事件便开始冒泡并触发事件处理程序执行。

### 自定义事件

通过createEvent()方法传入CustomEvent可以返回自定义事件对象，调用该对象的initCustomEvent()方法设定参数，之后调用dispatchEvent()方法可以触发事件。