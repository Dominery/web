# DOM事件

DOM允许JavaScript代码让HTML元素对事件作出反应，而这需要设置事件监听，将事件处理函数绑定在特定事件上，事件处理函数需要使用事件对象的相关属性获知到事件信息从而作出反应。

## 事件模型

### 监听函数

事件发生后，浏览器监听到了这个事件，就会执行对应的监听函数。

#### HTML的on-属性

HTML 语言允许在元素的属性中，直接定义某些事件的监听代码。

元素的事件监听属性，都是`on`加上事件名，比如`onload`就是`on + load`，表示`load`事件的监听代码。

* 这些属性的值是将会执行的代码，而不是一个函数。
* 使用这个方法指定的监听代码，只会在冒泡阶段触发。

#### 元素节点的事件属性

元素节点对象的事件属性，同样可以指定监听函数。使用这个方法指定的监听函数，也是只会在冒泡阶段触发。

* 如果定义两次`onclick`属性，后一次定义会覆盖前一次。

#### addEventListener()

所有 DOM 节点实例都有`addEventListener`方法，用来为该节点定义事件的监听函数。

### EventTarget接口

DOM 节点的事件操作（监听和触发），都定义在`EventTarget`接口。所有节点对象都部署了这个接口。

#### addEventListener()

通过addEventListenler()方法设置事件处理函数。

```javascript
oBox.addEventListener("click",()=>{
	//事件处理函数
},true);
```

该方法接受三个参数。

- `type`：事件名称，大小写敏感。
- `listener`：监听函数。事件发生时，会调用该监听函数。
- `useCapture`：布尔值，如果设为`true`，表示监听函数将在捕获阶段（capture）触发

第二个参数除了监听函数，还可以是一个具有`handleEvent`方法的对象。

第三个参数除了布尔值`useCapture`，还可以是一个监听器配置对象，定制事件监听行为。

> - `capture`：布尔值，如果设为`true`，表示监听函数在捕获阶段触发，默认为`false`，在冒泡阶段触发。
> - `once`：布尔值，如果设为`true`，表示监听函数执行一次就会自动移除，后面将不再监听该事件。该属性默认值为`false`。
> - `passive`：布尔值，设为`true`时，表示禁止监听函数调用`preventDefault()`方法。如果调用了，浏览器将忽略这个要求，并在控制台输出一条警告。该属性默认值为`false`。
> - `signal`：该属性的值为一个 AbortSignal 对象，为监听器设置了一个信号通道，用来在需要时发出信号，移除监听函数。

`addEventListener()`方法可以为针对当前对象的同一个事件，添加多个不同的监听函数。这些函数按照添加顺序触发，即先添加先触发。

如果为同一个事件多次添加同一个监听函数，该函数只会执行一次，多余的添加将自动被去除。

监听函数内部的`this`，指向当前事件所在的那个对象。

#### removeEventListener()

`EventTarget.removeEventListener()`方法用来移除`addEventListener()`方法添加的事件监听函数。该方法没有返回值。

`removeEventListener()`方法的参数，与`addEventListener()`方法完全一致。

* `removeEventListener()`方法移除的监听函数，必须是`addEventListener()`方法添加的那个监听函数，而且必须在同一个元素节点，否则无效。

#### dispatchEvent()

`dispatchEvent()`方法在当前节点上触发指定事件，从而触发监听函数的执行。该方法返回一个布尔值，只要有一个监听函数调用了`Event.preventDefault()`，则返回值为`false`，否则为`true`。

`dispatchEvent()`方法的参数是一个`Event`对象的实例

* 如果`dispatchEvent()`方法的参数为空，或者不是一个有效的事件对象，将报错。

### 事件传播

一个事件发生后，会在子元素和父元素之间传播（propagation）。这种传播分成三个阶段。

- 第一阶段：从window对象传导到目标节点（上层传到底层），称为“捕获阶段”（capture phase）。
- 第二阶段：在目标节点上触发，称为“目标阶段”（target phase）。
- 第三阶段：从目标节点传导回window对象（从底层传回上层），称为“冒泡阶段”（bubbling phase）。

没有冒泡的事件：onblur、onfocus、onmouseenter、onmouseleave。

#### 事件委托

如果批量添加事件监听或者动态绑定，每一个事件监听注册都会消耗一定的系统内存，而添加事件太多会导致监听数量太多，处理函数过多，内存消耗会非常大。事件委托用于解决这个问题。

由于事件会在冒泡阶段向上传播到父节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件。这称为事件委托。如果监听的事件不冒泡，则不能使用事件委托，例如onmouseenter。

如果希望事件到某个节点为止，不再传播，可以使用事件对象的`stopPropagation`方法。但是那个节点的其余监听函数依旧可以触发。

如果想要彻底取消该事件，不再触发后面所有`click`的监听函数，可以使用`stopImmediatePropagation`方法。

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

## Event对象

事件发生以后，会产生一个事件对象，作为参数传给监听函数。浏览器原生提供一个`Event`对象，所有的事件都是这个对象的实例。

### 创建

`Event`构造函数接受两个参数。第一个参数`type`是字符串，表示事件的名称；第二个参数`options`是一个对象，表示事件对象的配置。该对象主要有下面两个属性。

- `bubbles`：布尔值，可选，默认为`false`，表示事件对象是否冒泡。
- `cancelable`：布尔值，可选，默认为`false`，表示事件是否可以被取消，即能否用`Event.preventDefault()`取消这个事件。一旦事件被取消，就好像从来没有发生过，不会触发浏览器对该事件的默认行为。

如果不是显式指定`bubbles`属性为`true`，生成的事件就只能在“捕获阶段”触发监听函数。

### 实例属性

| 属性             | 说明                                                   |
| ---------------- | ------------------------------------------------------ |
| bubbles          | 表示当前事件是否会冒泡                                 |
| eventPhase       | 返回表示事件所处阶段的整数常量                         |
| cancelable       | 表示事件是否可以取消                                   |
| cancelBubble     | 如果设为`true`，相当于执行`stopPropagation()`          |
| defaultPrevented | 表示该事件是否调用过`preventDefault`                   |
| currentTarget    | 返回事件当前所在的节点                                 |
| target           | 返回原始触发事件的那个节点                             |
| type             | 表示事件类型                                           |
| timeStamp        | 返回一个相对于网页加载的毫秒时间戳，表示事件发生的时间 |
| isTrusted        | 表示该事件是否由真实的用户行为产生                     |
| detail           | 返回一个数值，表示事件的某种信息                       |

#### eventPhase

`Event.eventPhase`的返回值有四种可能。

- 0，事件目前没有发生。
- 1，事件目前处于捕获阶段，即处于从祖先节点向目标节点的传播过程中。
- 2，事件到达目标节点，即`Event.target`属性指向的那个节点。
- 3，事件处于冒泡阶段，即处于从目标节点向祖先节点的反向传播过程中。

#### detail

对于`click`和`dblclick`事件，`Event.detail`是鼠标按下的次数（`1`表示单击，`2`表示双击，`3`表示三击）；对于鼠标滚轮事件，`Event.detail`是滚轮正向滚动的距离，负值就是负向滚动的距离，返回值总是3的倍数。

### 实例方法

#### preventDefault()

`Event.preventDefault`方法取消浏览器对当前事件的默认行为。该方法生效的前提是，事件对象的`cancelable`属性为`true`，如果为`false`，调用该方法没有任何效果。

* 该方法只是取消事件对当前元素的默认影响，不会阻止事件的传播。

#### stopPropagation()

`stopPropagation`方法阻止事件在 DOM 中继续传播，防止再触发定义在别的节点上的监听函数，但是不包括在当前节点上其他的事件监听函数。

#### stopImmediatePropagation()

`Event.stopImmediatePropagation`方法阻止同一个事件的其他监听函数被调用，不管监听函数定义在当前节点还是其他节点。

#### composedPath()

`Event.composedPath()`返回一个数组，成员是事件的最底层节点和依次冒泡经过的所有上层节点。

## 鼠标事件

### 事件种类

鼠标事件主要有下面这些，所有事件都继承了`MouseEvent`接口

#### 点击事件

- `click`：按下鼠标（通常是按下主按钮）时触发。
- `dblclick`：在同一个元素上双击鼠标时触发。
- `mousedown`：按下鼠标键时触发。
- `mouseup`：释放按下的鼠标键时触发。

`click`事件可以看成是两个事件组成的：用户在同一个位置先触发`mousedown`，再触发`mouseup`。因此，触发顺序是，`mousedown`首先触发，`mouseup`接着触发，`click`最后触发。

双击时，`dblclick`事件则会在`mousedown`、`mouseup`、`click`之后触发。

#### 移动事件

- `mousemove`：当鼠标在一个节点内部移动时触发。当鼠标持续移动时，该事件会连续触发。为了避免性能问题，建议对该事件的监听函数做一些限定，比如限定一段时间内只能运行一次。
- `mouseenter`：鼠标进入一个节点时触发，进入子节点不会触发这个事件。
- `mouseover`：鼠标进入一个节点时触发，进入子节点会再一次触发这个事件。
- `mouseout`：鼠标离开一个节点时触发，离开内部子节点也会触发这个事件。
- `mouseleave`：鼠标离开一个节点时触发，离开内部子节点不会触发这个事件

`mouseout`事件和`mouseleave`事件，都是鼠标离开一个节点时触发。两者的区别是，在父元素内部离开一个子元素时，`mouseleave`事件不会触发，而`mouseout`事件会触发。

muoseenter不冒泡，mouseover冒泡。

#### 其他事件

- `contextmenu`：按下鼠标右键时（上下文菜单出现前）触发，或者按下“上下文”菜单键时触发。
- `wheel`：滚动鼠标的滚轮时触发，该事件继承的是`WheelEvent`接口。
- `selectstart`：鼠标选中文字

### MouseEvent接口

`MouseEvent`接口继承了`Event`接口，所以拥有`Event`的所有属性和方法，并且还提供鼠标独有的属性和方法。

#### 构造函数

`MouseEvent()`构造函数接受两个参数。第一个参数是字符串，表示事件名称；第二个参数是一个事件配置对象，该参数可选。除了`Event`接口的实例配置属性，该对象可以配置绝大部分实例属性，除movementX、movementY、offsetX、offsetY、pageX、pageY。

#### 实例属性

| 属性          | 说明                                                |
| ------------- | --------------------------------------------------- |
| altKey        | 是否同时按下 Alt 键                                 |
| ctrlKey       | 是否同时按下 Ctrl键                                 |
| metaKey       | 是否同时按下 Meta 键                                |
| shiftKey      | 是否同时按下 shift 键                               |
| button        | 返回一个数值，表示事件发生时按下了鼠标的哪个键      |
| buttons       | 回一个三个比特位的值，表示同时按下了哪些键          |
| clientX       | 返回鼠标位置相对于浏览器窗口左上角的水平坐标        |
| clientY       | 返回鼠标位置相对于浏览器窗口左上角的垂直坐标        |
| movementX     | 返回当前位置与上一个`mousemove`事件之间的水平距离   |
| movementY     | 返回当前位置与上一个`mousemove`事件之间的垂直距离   |
| screenX       | 返回鼠标位置相对于屏幕左上角的水平坐标              |
| screenY       | 返回鼠标位置相对于屏幕左上角的垂直坐标              |
| offsetX       | 返回鼠标位置与目标节点左侧的`padding`边缘的水平距离 |
| offsetY       | 返回鼠标位置与目标节点左侧的`padding`边缘的垂直距离 |
| pageX         | 返回鼠标位置与文档左侧边缘的距离                    |
| pageY         | 返回鼠标位置与文档上侧边缘的距离                    |
| relatedTarget | 返回事件的相关节点                                  |

##### button

- 0：按下主键（通常是左键），或者该事件没有初始化这个属性（比如`mousemove`事件）。
- 1：按下辅助键（通常是中键或者滚轮键）。
- 2：按下次键（通常是右键）。

##### buttons

- 1：二进制为`001`（十进制的1），表示按下左键。
- 2：二进制为`010`（十进制的2），表示按下右键。
- 4：二进制为`100`（十进制的4），表示按下中键或滚轮键。

同时按下多个键的时候，每个按下的键对应的比特位都会有值。

##### relatedTarget

| 事件名称   | target 属性    | relatedTarget 属性 |
| ---------- | -------------- | ------------------ |
| focusin    | 接受焦点的节点 | 丧失焦点的节点     |
| focusout   | 丧失焦点的节点 | 接受焦点的节点     |
| mouseenter | 将要进入的节点 | 将要离开的节点     |
| mouseleave | 将要离开的节点 | 将要进入的节点     |
| mouseout   | 将要离开的节点 | 将要进入的节点     |
| mouseover  | 将要进入的节点 | 将要离开的节点     |
| dragenter  | 将要进入的节点 | 将要离开的节点     |
| dragexit   | 将要离开的节点 | 将要进入的节点     |

#### 实例方法

##### getModifierState()

`MouseEvent.getModifierState`方法返回一个布尔值，表示有没有按下特定的功能键。它的参数是一个表示[功能键](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/getModifierState#Modifier_keys_on_Gecko)的字符串。

### WheelEvent接口

WheelEvent 接口继承了 MouseEvent 实例，代表鼠标滚轮事件的实例对象。鼠标滚轮相关的事件只有一个`wheel`事件，用户滚动鼠标的滚轮，就生成这个事件的实例。

#### 构造函数

`WheelEvent()`构造函数可以接受两个参数，第一个是字符串，表示事件类型，对于滚轮事件来说，这个值目前只能是`wheel`。第二个参数是事件的配置对象。该对象的属性除了`Event`、`UIEvent`的配置属性以外，还可以接受实例属性，所有属性都是可选的。

#### 实例属性

- `deltaX`：数值，表示滚轮的水平滚动量。
- `deltaY`：数值，表示滚轮的垂直滚动量。
- `deltaZ`：数值，表示滚轮的 Z 轴滚动量。
- `deltaMode`：数值，表示上面三个属性的单位，`0`是像素，`1`是行，`2`是页。

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



## 键盘事件

### 事件种类

#### keydown

按下键盘时触发

#### keypress

按下有值的键时触发，即按下 Ctrl、Alt、Shift、Meta 这样无值的键，这个事件不会触发。对于有值的键，按下时先触发`keydown`事件，再触发这个事件。

#### keyup

松开键盘时触发该事件。

### KeyboardEvent接口

`KeyboardEvent`接口用来描述用户与键盘的互动。这个接口继承了`Event`接口。

#### 构造函数

`KeyboardEvent`构造函数接受两个参数。第一个参数是字符串，表示事件类型；第二个参数是一个事件配置对象，该参数可选。除了`Event`接口提供的属性，还可以配置实例属性。

#### 实例属性

| 属性     | 说明                                           |
| -------- | ---------------------------------------------- |
| altKey   | 是否同时按下 Alt 键                            |
| ctrlKey  | 是否同时按下 Ctrl键                            |
| metaKey  | 是否同时按下 Meta 键                           |
| shiftKey | 是否同时按下 shift 键                          |
| code     | 返回一个字符串，表示当前按下的键的字符串形式   |
| key      | 返回一个字符串，表示按下的键名                 |
| location | 返回一个整数，表示按下的键处在键盘的哪一个区域 |
| repeat   | 代表该键是否被按着不放                         |
| charCode | 表示用户输入的字符的“字符码”                   |
| keyCode  | 表示用户按下的按键的“键码”                     |

##### code

- 数字键0 - 9：返回`digit0` - `digit9`
- 字母键A - z：返回`KeyA` - `KeyZ`
- 功能键F1 - F12：返回 `F1` - `F12`
- 方向键：返回`ArrowDown`、`ArrowUp`、`ArrowLeft`、`ArrowRight`
- Alt 键：返回`AltLeft`或`AltRight`
- Shift 键：返回`ShiftLeft`或`ShiftRight`
- Ctrl 键：返回`ControlLeft`或`ControlRight`

##### key

如果按下的键代表可打印字符，则返回这个字符，比如数字、字母。

如果按下的键代表不可打印的特殊字符，则返回预定义的键值，比如 Backspace，Tab，Enter，Shift，Control，Alt，CapsLock，Esc，Spacebar，PageUp，PageDown，End，Home，Left，Right，Up，Down，PrintScreen，Insert，Del，Win，F1～F12，NumLock，Scroll 等。

如果同时按下一个控制键和一个符号键，则返回符号键的键名。比如，按下 Ctrl + a，则返回`a`；按下 Shift + a，则返回大写的`A`。

如果无法识别键名，返回字符串`Unidentified`。

##### location

- 0：处在键盘的主区域，或者无法判断处于哪一个区域。
- 1：处在键盘的左侧，只适用那些有两个位置的键（比如 Ctrl 和 Shift 键）。
- 2：处在键盘的右侧，只适用那些有两个位置的键（比如 Ctrl 和 Shift 键）。
- 3：处在数字小键盘。

##### charCode

| 字符 | 字符码 |
| ---- | ------ |
| 0~9  | 48~57  |
| A~Z  | 65~90  |
| a~z  | 97~122 |

##### keyCode

| 按键              | 建码           |
| ----------------- | -------------- |
| 0~9               | 48~57          |
| a~z(不区分大小写) | 65~90          |
| ←↑→↓              | 37、38、39、40 |
| 回车              | 13             |
| 空格              | 32             |

#### 实例方法

##### getModifierState()

返回一个布尔值，表示是否按下或激活指定的功能键。它的常用参数如下。

- `Alt`：Alt 键
- `CapsLock`：大写锁定键
- `Control`：Ctrl 键
- `Meta`：Meta 键
- `NumLock`：数字键盘开关键
- `Shift`：Shift 键

## 进度事件

进度事件用来描述资源加载的进度，主要由 AJAX 请求、`<img>`、`<audio>`、`<video>`、`<style>`、`<link>`等外部资源的加载触发，继承了`ProgressEvent`接口。

### 事件种类

- `abort`：外部资源中止加载时（比如用户取消）触发。如果发生错误导致中止，不会触发该事件。
- `error`：由于错误导致外部资源无法加载时触发。
- `load`：外部资源加载成功时触发。
- `loadstart`：外部资源开始加载时触发。
- `loadend`：外部资源停止加载时触发，发生顺序排在`error`、`abort`、`load`等事件的后面。
- `progress`：外部资源加载过程中不断触发。
- `timeout`：加载超时时触发。

`loadend`事件的监听函数，可以用来取代`abort`事件、`load`事件、`error`事件的监听函数，因为它总是在这些事件之后发生。

`error`事件有一个特殊的性质，就是不会冒泡。

### ProgressEvent接口

`ProgressEvent`接口主要用来描述外部资源加载的进度。

#### 构造函数

`ProgressEvent()`构造函数接受两个参数。第一个参数是字符串，表示事件的类型，这个参数是必须的。第二个参数是一个配置对象，表示事件的属性，该参数可选。配置对象除了可以使用`Event`接口的配置属性，还可以使用实例属性。

#### 实例属性

- `lengthComputable`：布尔值，表示加载的总量是否可以计算，默认是`false`。
- `loaded`：整数，表示已经加载的量，默认是`0`。
- `total`：整数，表示需要加载的总量，默认是`0`。

## 表单事件

### 事件种类

#### input

`input`事件当`<input>`、`<select>`、`<textarea>`的值发生变化时触发。对于打开`contenteditable`属性的元素，只要值发生变化，也会触发`input`事件。

`input`事件的一个特点，就是会连续触发，比如用户每按下一次按键，就会触发一次`input`事件。

**input和change**

`input`事件在元素的值发生变化后立即发生，而`change`在元素失去焦点时发生，而内容此时可能已经变化多次。

#### select

`select`事件当在`<input>`、`<textarea>`里面选中文本时触发。

选中的文本可以通过`event.target`元素的`selectionDirection`、`selectionEnd`、`selectionStart`和`value`属性拿到。

#### change

`change`事件当`<input>`、`<select>`、`<textarea>`的值发生变化时触发。

- 激活单选框（radio）或复选框（checkbox）时触发。
- 用户提交时触发。比如，从下列列表（select）完成选择，在日期或文件输入框完成选择。
- 当文本框或`<textarea>`元素的值发生改变，并且丧失焦点时触发。

#### invalid

用户提交表单时，如果表单元素的值不满足校验条件，就会触发`invalid`事件。

#### reset, submit

这两个事件发生在表单对象`<form>`上，而不是发生在表单的成员上。

`reset`事件当表单重置（所有表单成员变回默认值）时触发。

`submit`事件当表单数据向服务器提交时触发。

### InputEvent接口

`InputEvent`接口主要用来描述`input`事件的实例。该接口继承了`Event`接口。

#### 构造函数

`InputEvent`构造函数可以接受两个参数。第一个参数是字符串，表示事件名称，该参数是必需的。第二个参数是一个配置对象，用来设置事件实例的属性，该参数是可选的。配置对象的字段除了`Event`构造函数的配置属性，可以使用实例属性。

#### 实例属性

##### data

返回一个字符串，表示变动的内容。

##### inputType

返回一个字符串，表示字符串发生变更的类型。

对于常见情况，Chrome 浏览器的返回值如下。完整列表可以参考[文档](https://w3c.github.io/input-events/index.html#dom-inputevent-inputtype)。

- 手动插入文本：`insertText`
- 粘贴插入文本：`insertFromPaste`
- 向后删除：`deleteContentBackward`
- 向前删除：`deleteContentForward`

##### dataTransfer

返回一个 DataTransfer 实例。该属性只在文本框接受粘贴内容（insertFromPaste）或拖拽内容（`insertFromDrop`）时才有效。

## 触摸事件

浏览器的触摸 API 由三个部分组成。

- Touch：一个触摸点
- TouchList：多个触摸点的集合
- TouchEvent：触摸引发的事件实例

`Touch`接口的实例对象用来表示触摸点，多个触摸点的集合由`TouchList`接口的实例对象表示。`TouchEvent`接口的实例对象代表由触摸引发的事件，只有触摸屏才会引发这一类事件。

### 事件种类

- `touchstart`：用户开始触摸时触发，它的`target`属性返回发生触摸的元素节点。
- `touchend`：用户不再接触触摸屏时（或者移出屏幕边缘时）触发，它的`target`属性与`touchstart`事件一致的，就是开始触摸时所在的元素节点。它的`changedTouches`属性返回一个`TouchList`实例，包含所有不再触摸的触摸点（即`Touch`实例对象）。
- `touchmove`：用户移动触摸点时触发，它的`target`属性与`touchstart`事件一致。如果触摸的半径、角度、力度发生变化，也会触发该事件。
- `touchcancel`：触摸点取消时触发，比如在触摸区域跳出一个模态窗口（modal window）、触摸点离开了文档区域（进入浏览器菜单栏）、用户的触摸点太多，超过了支持的上限（自动取消早先的触摸点）。

### Touch接口

#### 构造函数

`Touch`构造函数接受一个配置对象作为参数，字段可以使用实例属性。

#### 实例属性

| 属性          | 说明                                               |
| ------------- | -------------------------------------------------- |
| identifier    | 返回一个整数，表示触摸点的唯一 ID                  |
| screenX       | 返回触摸点相对于屏幕左上角的水平坐标               |
| screenY       | 返回触摸点相对于屏幕左上角的垂直坐标               |
| clientX       | 返回触摸点相对于浏览器窗口左上角的水平坐标         |
| clientY       | 返回触摸点相对于浏览器窗口左上角的垂直坐标         |
| pageX         | 触摸点相对于当前页面左上角的横坐标                 |
| pageY         | 触摸点相对于当前页面左上角的纵坐标                 |
| radiusX       | 返回触摸点周围受到影响的椭圆范围的 X 轴半径        |
| radiusY       | 返回触摸点周围受到影响的椭圆范围的 Y 轴半径        |
| rotationAngle | 表示触摸区域的椭圆的旋转角度，在`0`到`90`度之间    |
| force         | 返回一个`0`到`1`之间的数值，表示触摸压力           |
| target        | 返回一个元素节点，代表触摸发生时所在的那个元素节点 |

### TouchList接口

`TouchList`接口表示一组触摸点的集合。它的实例是一个类似数组的对象，成员是`Touch`的实例对象，表示所有触摸点。

它的实例主要通过触摸事件的`TouchEvent.touches`、`TouchEvent.changedTouches`、`TouchEvent.targetTouches`这几个属性获取

它的实例属性和实例方法只有两个。

- `TouchList.length`：数值，表示成员数量（即触摸点的数量）。
- `TouchList.item()`：返回指定位置的成员，它的参数是该成员的位置编号（从零开始）。

### TouchEvent接口

TouchEvent 接口继承了 Event 接口，表示由触摸引发的事件实例，通常来自触摸屏或轨迹板。

#### 构造函数

`TouchEvent()`构造函数可以接受两个参数，第一个参数是字符串，表示事件类型；第二个参数是事件的配置对象，该参数是可选的，对象的所有属性也是可选的。

#### 实例属性

| 属性           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| altKey         | 是否同时按下 Alt 键                                          |
| ctrlKey        | 是否同时按下 Ctrl键                                          |
| metaKey        | 是否同时按下 Meta 键                                         |
| shiftKey       | 是否同时按下 shift 键                                        |
| changedTouches | 返回一个`TouchList`实例，表示本次触摸事件的相关触摸点        |
| touches        | 返回一个`TouchList`实例，成员是所有仍然处于活动状态的触摸点  |
| targetTouches  | 返回一个`TouchList`实例，成员是触摸事件的目标元素节点内部、所有仍然处于活动状态（即触摸中）的触摸点 |

##### changedTouches

对于不同的时间，该属性的含义有所不同。

- `touchstart`事件：被激活的触摸点
- `touchmove`事件：发生变化的触摸点
- `touchend`事件：消失的触摸点（即不再被触碰的点）

## 拖拉事件

拖拉（drag）指的是，用户在某个对象上按下鼠标键不放，拖动它到另一个位置，然后释放鼠标键，将该对象放在那里。在网页中，除了元素节点默认不可以拖拉，其他（图片、链接、选中的文字）都可以直接拖拉。

为了让元素节点可拖拉，可以将该节点的`draggable`属性设为`true`。

### 事件种类

- `drag`：拖拉过程中，在被拖拉的节点上持续触发（相隔几百毫秒）。
- `dragstart`：用户开始拖拉时，在被拖拉的节点上触发，该事件的`target`属性是被拖拉的节点。通常应该在这个事件的监听函数中，指定拖拉的数据。
- `dragend`：拖拉结束时（释放鼠标键或按下 ESC 键）在被拖拉的节点上触发，该事件的`target`属性是被拖拉的节点。它与`dragstart`事件，在同一个节点上触发。不管拖拉是否跨窗口，或者中途被取消，`dragend`事件总是会触发的。
- `dragenter`：拖拉进入当前节点时，在当前节点上触发一次，该事件的`target`属性是当前节点。通常应该在这个事件的监听函数中，指定是否允许在当前节点放下（drop）拖拉的数据。如果当前节点没有该事件的监听函数，或者监听函数不执行任何操作，就意味着不允许在当前节点放下数据。在视觉上显示拖拉进入当前节点，也是在这个事件的监听函数中设置。
- `dragover`：拖拉到当前节点上方时，在当前节点上持续触发（相隔几百毫秒），该事件的`target`属性是当前节点。该事件与`dragenter`事件的区别是，`dragenter`事件在进入该节点时触发，然后只要没有离开这个节点，`dragover`事件会持续触发。
- `dragleave`：拖拉操作离开当前节点范围时，在当前节点上触发，该事件的`target`属性是当前节点。如果要在视觉上显示拖拉离开操作当前节点，就在这个事件的监听函数中设置。
- `drop`：被拖拉的节点或选中的文本，释放到目标节点时，在目标节点上触发。注意，如果当前节点不允许`drop`，即使在该节点上方松开鼠标键，也不会触发该事件。如果用户按下 ESC 键，取消这个操作，也不会触发该事件。该事件的监听函数负责取出拖拉数据，并进行相关处理。

关于拖拉事件，有以下几个注意点。

- 拖拉过程只触发以上这些拖拉事件，尽管鼠标在移动，但是鼠标事件不会触发。
- 将文件从操作系统拖拉进浏览器，不会触发`dragstart`和`dragend`事件。
- `dragenter`和`dragover`事件的监听函数，用来取出拖拉的数据（即允许放下被拖拉的元素）。如果想要在目标节点上放下的数据，首先必须阻止这两个事件的默认行为。

### DragEvent接口

拖拉事件都继承了`DragEvent`接口，这个接口又继承了`MouseEvent`接口和`Event`接口。

`DragEvent()`构造函数接受两个参数，第一个参数是字符串，表示事件的类型，该参数必须；第二个参数是事件的配置对象，用来设置事件的属性，该参数可选。配置对象除了接受`MouseEvent`接口和`Event`接口的配置属性，还可以设置`dataTransfer`属性要么是`null`，要么是一个`DataTransfer`接口的实例。

`DataTransfer`的实例对象用来读写拖拉事件中传输的数据。

### DataTransfer接口

所有拖拉事件的实例都有一个`DragEvent.dataTransfer`属性，用来读写需要传递的数据。这个属性的值是一个`DataTransfer`接口的实例。

`DataTransfer()`构造函数不接受参数。

#### 实例属性

##### dropEffect

`DataTransfer.dropEffect`属性用来设置放下（drop）被拖拉节点时的效果，会影响到拖拉经过相关区域时鼠标的形状。

它可能取下面的值。

- copy：复制被拖拉的节点
- move：移动被拖拉的节点
- link：创建指向被拖拉的节点的链接
- none：无法放下被拖拉的节点

`dropEffect`属性一般在`dragenter`和`dragover`事件的监听函数中设置，对于`dragstart`、`drag`、`dragleave`这三个事件，该属性不起作用。

##### effectAllowed

`DataTransfer.effectAllowed`属性设置本次拖拉中允许的效果。它可能取下面的值。

- copy：复制被拖拉的节点
- move：移动被拖拉的节点
- link：创建指向被拖拉节点的链接
- copyLink：允许`copy`或`link`
- copyMove：允许`copy`或`move`
- linkMove：允许`link`或`move`
- all：允许所有效果
- none：无法放下被拖拉的节点
- uninitialized：默认值，等同于`all`

这个属性与`dropEffect`属性是同一件事的两个方面。前者设置被拖拉的节点允许的效果，后者设置接受拖拉的区域的效果，它们往往配合使用。

`dragstart`事件的监听函数，可以用来设置这个属性。其他事件的监听函数里面设置这个属性是无效的。

##### files

`DataTransfer.files`属性是一个 FileList 对象，包含一组本地文件，可以用来在拖拉操作中传送。如果本次拖拉不涉及文件，则该属性为空的 FileList 对象。

##### types

`DataTransfer.types`属性是一个只读的数组，每个成员是一个字符串，里面是拖拉的数据格式（通常是 MIME 值）。

##### items

`DataTransfer.items`属性返回一个类似数组的只读对象（DataTransferItemList 实例），每个成员就是本次拖拉的一个对象（DataTransferItem 实例）。如果本次拖拉不包含对象，则返回一个空对象。

DataTransferItemList 实例具有以下的属性和方法。

- `length`：返回成员的数量
- `add(data, type)`：增加一个指定内容和类型（比如`text/html`和`text/plain`）的字符串作为成员
- `add(file)`：`add`方法的另一种用法，增加一个文件作为成员
- `remove(index)`：移除指定位置的成员
- `clear()`：移除所有的成员

DataTransferItem 实例具有以下的属性和方法。

- `kind`：返回成员的种类（`string`还是`file`）。
- `type`：返回成员的类型（通常是 MIME 值）。
- `getAsFile()`：如果被拖拉是文件，返回该文件，否则返回`null`。
- `getAsString(callback)`：如果被拖拉的是字符串，将该字符传入指定的回调函数处理。该方法是异步的，所以需要传入回调函数。

#### 实例方法

##### setData()

`DataTransfer.setData()`方法用来设置拖拉事件所带有的数据。

该方法接受两个参数，都是字符串。第一个参数表示数据类型（比如`text/plain`），第二个参数是具体数据。如果指定类型的数据在`dataTransfer`属性不存在，那么这些数据将被加入，否则原有的数据将被新数据替换。

##### getData()

`DataTransfer.getData()`方法接受一个字符串（表示数据类型）作为参数，返回事件所带的指定类型的数据（通常是用`setData`方法添加的数据）。如果指定类型的数据不存在，则返回空字符串。通常只有`drop`事件触发后，才能取出数据。

类型值指定为`URL`，可以取出第一个有效链接。

##### clearData()

`DataTransfer.clearData()`方法接受一个字符串（表示数据类型）作为参数，删除事件所带的指定类型的数据。如果没有指定类型，则删除所有数据。如果指定类型不存在，则调用该方法不会产生任何效果。该方法不会移除拖拉的文件。

该方法只能在`dragstart`事件的监听函数之中使用，因为这是拖拉操作的数据唯一可写的时机。

##### setDragImage()

拖动过程中（`dragstart`事件触发后），浏览器会显示一张图片跟随鼠标一起移动，表示被拖动的节点。

`DataTransfer.setDragImage()`方法可以自定义这张图片。它接受三个参数。第一个是`<img>`节点或者`<canvas>`节点，如果省略或为`null`，则使用被拖动的节点的外观；第二个和第三个参数为鼠标相对于该图片左上角的横坐标和纵坐标。

## 其他事件

### 资源事件

#### beforeunload

`beforeunload`事件在窗口、文档、各种资源将要卸载前触发。它可以用来防止用户不小心卸载资源。

如果该事件对象的`returnValue`属性是一个非空字符串，那么浏览器就会弹出一个对话框，询问用户是否要卸载该资源。

#### unload

`unload`事件在窗口关闭或者`document`对象将要卸载时触发。它的触发顺序排在`beforeunload`、`pagehide`事件后面。

`unload`事件发生时，文档处于一个特殊状态。所有资源依然存在，但是对用户来说都不可见，UI 互动全部无效。这个事件是无法取消的，即使在监听函数里面抛出错误，也不能停止文档的卸载。

#### load, error, abort

`load`事件在页面或某个资源加载成功时触发。注意，页面或资源从浏览器缓存加载，并不会触发`load`事件。如果是从缓存中加载页面，网页内初始化的 JavaScript 脚本（比如 DOMContentLoaded 事件的监听函数）也不会执行。

`error`事件是在页面或资源加载失败时触发。`abort`事件在用户取消加载时触发。

这三个事件实际上属于进度事件，不仅发生在`document`对象，还发生在各种外部资源上面。

页面的`load`事件也可以用`pageshow`事件代替。

### session历史事件

#### pageshow

默认情况下，浏览器会在当前会话（session）缓存页面，当用户点击“前进/后退”按钮时，浏览器就会从缓存中加载页面。

`pageshow`事件在页面加载时触发，包括第一次加载和从缓存加载两种情况。如果要指定页面每次加载（不管是不是从浏览器缓存）时都运行的代码，可以放在这个事件的监听函数。

第一次加载时，它的触发顺序排在`load`事件后面。

`pageshow`事件有一个`persisted`属性，返回一个布尔值。页面第一次加载时，这个属性是`false`；当页面从缓存加载时，这个属性是`true`。

#### pagehide

`pagehide`事件与`pageshow`事件类似，当用户通过“前进/后退”按钮，离开当前页面时触发。它与 unload 事件的区别在于，如果在 window 对象上定义`unload`事件的监听函数之后，页面不会保存在缓存中，而使用`pagehide`事件，页面会保存在缓存中。

`pagehide`事件实例也有一个`persisted`属性，将这个属性设为`true`，就表示页面要保存在缓存中；设为`false`，表示网页不保存在缓存中，这时如果设置了unload 事件的监听函数，该函数将在 pagehide 事件后立即运行。

如果页面包含`<frame>`或`<iframe>`元素，则`<frame>`页面的`pageshow`事件和`pagehide`事件，都会在主页面之前触发。

#### popstate

`popstate`事件在浏览器的`history`对象的当前记录发生显式切换时触发。注意，调用`history.pushState()`或`history.replaceState()`，并不会触发`popstate`事件。该事件只在用户在`history`记录之间显式切换时触发.

该事件对象有一个`state`属性，保存`history.pushState`方法和`history.replaceState`方法为当前记录添加的`state`对象。

#### hashchange

`hashchange`事件在 URL 的 hash 部分（即`#`号后面的部分，包括`#`号）发生变化时触发。该事件一般在`window`对象上监听。

`hashchange`的事件实例具有两个特有属性：`oldURL`属性和`newURL`属性，分别表示变化前后的完整 URL。

### 网页状态事件

#### DOMContentLoaded

网页下载并解析完成以后，浏览器就会在`document`对象上触发 DOMContentLoaded 事件。这时，仅仅完成了网页的解析（整张页面的 DOM 生成了），所有外部资源（样式表、脚本、iframe 等等）可能还没有下载结束。

#### readystatechange

`readystatechange`事件当 Document 对象和 XMLHttpRequest 对象的`readyState`属性发生变化时触发。`document.readyState`有三个可能的值：`loading`（网页正在加载）、`interactive`（网页已经解析完成，但是外部资源仍然处在加载状态）和`complete`（网页和所有外部资源已经结束加载，`load`事件即将触发）。

### 窗口事件

#### scroll

`scroll`事件在文档或文档元素滚动时触发，主要出现在用户拖动滚动条。

该事件会连续地大量触发，所以它的监听函数之中不应该有非常耗费计算的操作。

#### resize

`resize`事件在改变浏览器窗口大小时触发，主要发生在`window`对象上面。该事件也会连续地大量触发。

#### fullscreenchange, fullscreenerror

`fullscreenchange`事件在进入或退出全屏状态时触发，该事件发生在`document`对象上面。`fullscreenerror`事件在浏览器无法切换到全屏状态时触发。

### 剪切板事件

以下三个事件属于剪贴板操作的相关事件。

- `cut`：将选中的内容从文档中移除，加入剪贴板时触发。
- `copy`：进行复制动作时触发。
- `paste`：剪贴板内容粘贴到文档后触发。

`cut`、`copy`、`paste`这三个事件的事件对象都是`ClipboardEvent`接口的实例。`ClipboardEvent`有一个实例属性`clipboardData`，是一个 DataTransfer 对象，存放剪贴的数据。

### 焦点事件

- `focus`：元素节点获得焦点后触发，该事件不会冒泡。
- `blur`：元素节点失去焦点后触发，该事件不会冒泡。
- `focusin`：元素节点将要获得焦点时触发，发生在`focus`事件之前。该事件会冒泡。
- `focusout`：元素节点将要失去焦点时触发，发生在`blur`事件之前。该事件会冒泡。

这四个事件的事件对象都继承了`FocusEvent`接口。`FocusEvent`实例具有以下属性。

- `FocusEvent.target`：事件的目标节点。
- `FocusEvent.relatedTarget`：对于`focusin`事件，返回失去焦点的节点；对于`focusout`事件，返回将要接受焦点的节点；对于`focus`和`blur`事件，返回`null`。

### CustomEvent接口

CustomEvent 接口用于生成自定义的事件实例。那些浏览器预定义的事件，虽然可以手动生成，但是往往不能在事件上绑定数据。如果需要在触发事件的同时，传入指定的数据，就可以使用 CustomEvent 接口生成的自定义事件对象。

#### 构造函数

`CustomEvent()`构造函数接受两个参数。第一个参数是字符串，表示事件的名字，这是必须的。第二个参数是事件的配置对象，这个参数是可选的。`CustomEvent`的配置对象除了接受 Event 事件的配置属性，只有一个自己的属性。

- `detail`：表示事件的附带数据，默认为`null`。