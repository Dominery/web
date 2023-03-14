# 浏览器环境

## 代码嵌入

### 代码嵌入网页

网页中嵌入 JavaScript 代码，主要有四种方法。

- `<script>`元素直接嵌入代码。
- `<script>`标签加载外部脚本
- 事件属性
- URL 协议

#### script元素嵌入代码

`<script>`元素内部可以直接写入 JavaScript 代码。

`<script>`标签有一个type属性，用来指定脚本类型。对 JavaScript 脚本来说，type属性可以设为两种值。

* text/javascript：默认值。
* application/javascript：对于较新的浏览器，建议设为这个值。

如果`type`属性的值，浏览器不认识，那么它不会执行其中的代码。

#### script元素加载外部脚本

如果脚本文件使用了非英语字符，还应该注明字符的编码。

加载外部脚本和直接添加代码块，这两种方法不能混用。

为了防止攻击者篡改外部脚本，`script`标签允许设置一个`integrity`属性，写入该外部脚本的 Hash 签名，用来验证脚本的一致性。

#### 事件属性

网页元素的事件属性（比如`onclick`和`onmouseover`），可以写入 JavaScript 代码。当指定事件发生时，就会调用这些代码。

#### URL协议

URL 支持`javascript:`协议，即在 URL 的位置写入代码，使用这个 URL 的时候就会执行 JavaScript 代码。

```html
<a href="javascript:console.log('Hello')">点击</a>
```

如果 JavaScript 代码返回一个字符串，浏览器就会新建一个文档，展示这个字符串的内容，原有文档的内容都会消失。

### script元素

#### 工作原理

浏览器加载 JavaScript 脚本，主要通过`<script>`元素完成。正常的网页加载流程是这样的。

1. 浏览器一边下载 HTML 网页，一边开始解析。也就是说，不等到下载完，就开始解析。
2. 解析过程中，浏览器发现`<script>`元素，就暂停解析，把网页渲染的控制权转交给 JavaScript 引擎。
3. 如果`<script>`元素引用了外部脚本，就下载该脚本再执行，否则就直接执行代码。
4. JavaScript 引擎执行完毕，控制权交还渲染引擎，恢复往下解析 HTML 网页。

如果外部脚本加载时间很长（一直无法完成下载），那么浏览器就会一直等待脚本下载完成，造成网页长时间失去响应，浏览器就会呈现“假死”状态，这被称为“阻塞效应”。

为了避免这种情况，较好的做法是将`<script>`标签都放在页面底部。

#### 属性

| 属性     | 说明                                                     |
| -------- | -------------------------------------------------------- |
| async    | 表示并行下载脚本，下载完执行。                           |
| charset  | 表示通过src属性指定的代码的字符集。                      |
| defer    | 表示并行下载，渲染结束后执行。                           |
| language | 已废弃。原来用于表示编写代码使用的脚本语言               |
| src      | 表示包含要执行代码的外部文件。                           |
| type     | 表示编写代码使用的脚本语言的内容类型（也称为MIME类型）。 |

> 大多数浏览器会忽略charset的值，因此这个属性很少有人用。

##### defer

添加`defer`属性，浏览器下载脚本文件的时候，不会阻塞页面渲染。下载的脚本文件在`DOMContentLoaded`事件触发前执行（即刚刚读取完`</html>`标签），而且可以保证执行顺序就是它们在页面上出现的顺序。

对于内置而不是加载外部脚本的`script`标签，以及动态生成的`script`标签，`defer`属性不起作用。

##### async

`async`属性可以保证脚本下载的同时，浏览器继续渲染。需要注意的是，一旦采用这个属性，就无法保证脚本的执行顺序。哪个脚本先下载结束，就先执行那个脚本。

如果同时使用`async`和`defer`属性，后者不起作用，浏览器行为由`async`属性决定。

##### src

src中可以指定加载协议，默认HTTP协议。

根据页面本身协议加载。

```html
<script src="//example.js"></script>
```

#### 动态加载

`<script>`元素还可以动态生成，生成后再插入页面，从而实现脚本的动态加载。

动态生成的`script`标签不会阻塞页面渲染，也就不会造成浏览器假死。但是问题在于，这种方法无法保证脚本的执行顺序。

如果想避免这个问题，可以设置async属性为`false`。

## window对象

浏览器里面，`window`对象指当前的浏览器窗口。它也是当前页面的顶层对象，即最高一层的对象，所有其他对象都是它的下属。一个变量如果未声明，那么默认就是顶层对象的属性。

### 属性

| 属性             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| name             | 字符串，如果写入的值不是字符串，会自动转成字符串             |
| closed           | 表示窗口是否关闭                                             |
| opener           | 打开当前窗口的父窗口                                         |
| self             | 当前窗口                                                     |
| window           | 当前窗口                                                     |
| frames           | 返回类数组对象，成员为页面内所有框架窗口                     |
| length           | 返回当前网页包含的框架总数                                   |
| frameElement     | 当前窗口嵌在另一个网页，返回当前窗口所在的那个元素节点，顶层返回null |
| top              | 最顶层窗口                                                   |
| parent           | 父窗口。顶层返回自身。                                       |
| status           | 用于读写浏览器状态栏的文本                                   |
| devicePixelRatio | 返回一个数值，表示一个 CSS 像素的大小与一个物理像素的大小之间的比率。 |
| isSecureContext  | 返回一个布尔值，表示当前窗口是否处在加密环境。如果是 HTTPS 协议，就是`true`，否则就是`false`。 |

#### name

只要浏览器窗口不关闭，这个属性是不会消失的。举例来说，访问`a.com`时，该页面的脚本设置了`window.name`，接下来在同一个窗口里面载入了`b.com`，新页面的脚本可以读到上一个网页设置的`window.name`。

#### opener

如果当前窗口没有父窗口（即直接在地址栏输入打开），则返回`null`。

通过`opener`属性，可以获得父窗口的全局属性和方法，但只限于两个窗口同源的情况。

`<a>`元素添加rel="noopener"属性，可以防止新打开的窗口获取父窗口。

#### frames

如果`iframe`元素设置了`id`或`name`属性，那么就可以用属性值，引用这个`iframe`窗口。

`frames`属性实际上是`window`对象的别名。

#### 位置大小属性

##### screenX, screenY

`window.screenX`和`window.screenY`属性，返回浏览器窗口左上角相对于当前屏幕左上角的水平距离和垂直距离（单位像素）。

##### innerHeight, innerWidth

`window.innerHeight`和`window.innerWidth`属性，返回网页在当前窗口中可见部分的高度和宽度。

用户放大网页的时候（比如将网页从100%的大小放大为200%），这两个属性会变小。因为这时网页的像素大小不变（比如宽度还是960像素），只是每个像素占据的屏幕空间变大了，因此可见部分（视口）就变小了。

##### outerHeight, outerWidth

`window.outerHeight`和`window.outerWidth`属性返回浏览器窗口的高度和宽度，包括浏览器菜单和边框（单位像素）。

##### scrollX, scrollY

`window.scrollX`属性返回页面的水平滚动距离，`window.scrollY`属性返回页面的垂直滚动距离，单位都为像素。

`window.pageXOffset`属性和`window.pageYOffset`属性，是`window.scrollX`和`window.scrollY`别名。

#### 组件属性

- `window.locationbar`：地址栏对象
- `window.menubar`：菜单栏对象
- `window.scrollbars`：窗口的滚动条对象
- `window.toolbar`：工具栏对象
- `window.statusbar`：状态栏对象
- `window.personalbar`：用户安装的个人工具栏对象

#### 全局对象属性

全局对象属性指向一些浏览器原生的全局对象。

- `window.document`：指向`document`对象，这个属性有同源限制。只有来自同源的脚本才能读取这个属性。
- `window.location`：指向`Location`对象，用于获取当前窗口的 URL 信息。它等同于`document.location`属性。
- `window.navigator`：指向`Navigator`对象，用于获取环境信息。
- `window.history`：指向`History`对象，表示浏览器的浏览历史。
- `window.localStorage`：指向本地储存的 localStorage 数据。
- `window.sessionStorage`：指向本地储存的 sessionStorage 数据。
- `window.console`：指向`console`对象，用于操作控制台。
- `window.screen`：指向`Screen`对象，表示屏幕信息。

### 方法

#### alert(), prompt(), confirm()

`window.alert()`、`window.prompt()`、`window.confirm()`都是浏览器与用户互动的全局方法。它们会弹出不同的对话框，要求用户做出回应。

`window.alert()`方法弹出的对话框，只有一个“确定”按钮，往往用来通知用户某些信息。

**prompt()**

`window.prompt()`方法弹出的对话框，提示文字的下方，还有一个输入框，要求用户输入信息，并有“确定”和“取消”两个按钮。

`window.prompt()`的返回值有两种情况，可能是字符串（有可能是空字符串），也有可能是`null`。具体分成三种情况。

1. 用户输入信息，并点击“确定”，则用户输入的信息就是返回值。
2. 用户没有输入信息，直接点击“确定”，则输入框的默认值就是返回值。
3. 用户点击了“取消”（或者按了 ESC 按钮），则返回值是`null`。

`window.prompt()`方法的第二个参数是可选的，但是最好总是提供第二个参数，作为输入框的默认值。

**confirm()**

`window.confirm()`方法弹出的对话框，除了提示信息之外，只有“确定”和“取消”两个按钮，往往用来征询用户是否同意。

`confirm`方法返回一个布尔值，如果用户点击“确定”，返回`true`；如果用户点击“取消”，则返回`false`。

#### open(), close(), stop()

**open()**

`window.open`方法用于新建另一个浏览器窗口，类似于浏览器菜单的新建窗口选项。它会返回新窗口的引用，如果无法新建窗口，则返回`null`。

`open`方法一共可以接受三个参数。

- `url`：字符串，表示新窗口的网址。如果省略，默认网址就是`about:blank`。
- `windowName`：字符串，表示新窗口的名字。如果该名字的窗口已经存在且同源，则占用该窗口，不再新建窗口。如果省略，就默认使用`_blank`，表示新建一个没有名字的窗口。另外还有几个预设值，`_self`表示当前窗口，`_top`表示顶层窗口，`_parent`表示上一层窗口。
- `windowFeatures`：字符串，内容为逗号分隔的键值对，表示新窗口的参数，比如有没有提示栏、工具条等等。如果省略，则默认打开一个完整 UI 的新窗口。

**close()**

一般只用来关闭`window.open`方法新建的窗口。

**stop()**

`window.stop()`方法完全等同于单击浏览器的停止按钮，会停止加载图像、视频等正在或等待加载的对象。

#### moveTo(), moveBy()

只有一种情况，浏览器允许用脚本移动窗口：该窗口是用`window.open()`方法新建的，并且窗口里只有它一个 Tab 页。

**moveTo()**

`window.moveTo()`方法用于移动浏览器窗口到指定位置。它接受两个参数，分别是窗口左上角距离屏幕左上角的水平距离和垂直距离。

**moveBy()**

`window.moveBy()`方法将窗口移动到一个相对位置。它接受两个参数，分别是窗口左上角向右移动的水平距离和向下移动的垂直距离

#### resizeTo(), resizeBy()

**resizeTo()**

`window.resizeTo()`方法用于缩放窗口到指定大小。

它接受两个参数，第一个是缩放后的窗口宽度（`outerWidth`属性，包含滚动条、标题栏等等），第二个是缩放后的窗口高度（`outerHeight`属性）。

**resizeBy()**

`window.resizeBy()`方法用于缩放窗口。它与`window.resizeTo()`的区别是，它按照相对的量缩放，`window.resizeTo()`需要给出缩放后的绝对大小

#### scrollTo(), scroll(), scrollBy()

**scrollTo()**

`window.scrollTo`方法用于将文档滚动到指定位置。它接受两个参数，表示滚动后位于窗口左上角的页面坐标。

它也可以接受一个配置对象作为参数。

配置对象`options`有三个属性。

- `top`：滚动后页面左上角的垂直坐标，即 y 坐标。
- `left`：滚动后页面左上角的水平坐标，即 x 坐标。
- `behavior`：字符串，表示滚动的方式，有三个可能值（`smooth`、`instant`、`auto`），默认值为`auto`。

`window.scroll()`方法是`window.scrollTo()`方法的别名。

**scrollBy()**

`window.scrollBy()`方法用于将网页滚动指定距离（单位像素）。它接受两个参数：水平向右滚动的像素，垂直向下滚动的像素。

#### print()

`window.print`方法会跳出打印对话框，与用户点击菜单里面的“打印”命令效果相同。

#### focus(), blur()

`window.focus()`方法会激活窗口，使其获得焦点，出现在其他窗口的前面。

`window.blur()`方法将焦点从窗口移除。

当前窗口获得焦点时，会触发`focus`事件；当前窗口失去焦点时，会触发`blur`事件。

#### getSelection()

`window.getSelection`方法返回一个`Selection`对象，表示用户现在选中的文本。

#### getComputedStyle(), matchMedia()

`window.getComputedStyle()`方法接受一个元素节点作为参数，返回一个包含该元素的最终样式信息的对象。

`window.matchMedia()`方法用来检查 CSS 的`mediaQuery`语句。

#### requestAnimationFrame()

该方法接受一个回调函数作为参数。`callback`执行时，它的参数就是系统传入的一个高精度时间戳（`performance.now()`的返回值）。

`window.requestAnimationFrame()`的返回值是一个整数，这个整数可以传入`window.cancelAnimationFrame()`，用来取消回调函数的执行。

`window.requestAnimationFrame()`推迟回调函数到浏览器下一次回流时执行，执行完才会进行下一次重绘。重绘通常是 16ms 执行一次，不过浏览器会自动调节这个速率，比如网页切换到后台 Tab 页时，`requestAnimationFrame()`会暂停执行。

#### requestIdleCallback()

`window.requestIdleCallback()`跟`setTimeout`类似，也是将某个函数推迟执行，但是它保证将回调函数推迟到系统资源空闲时执行。

该方法接受一个回调函数和一个配置对象作为参数。配置对象可以指定一个推迟执行的最长时间，如果过了这个时间，回调函数不管系统资源有无空闲，都会执行。

`callback`参数是一个回调函数。该回调函数执行时，系统会传入一个`IdleDeadline`对象作为参数。`IdleDeadline`对象有一个`didTimeout`属性（布尔值，表示是否为超时调用）和一个`timeRemaining()`方法（返回该空闲时段剩余的毫秒数）。

`options`参数是一个配置对象，目前只有`timeout`一个属性，用来指定回调函数推迟执行的最大毫秒数。该参数可选。

`window.requestIdleCallback()`方法返回一个整数。该整数可以传入`window.cancelIdleCallback()`取消回调函数。

### screen对象

- `Screen.height`：浏览器窗口所在的屏幕的高度（单位像素）。
- `Screen.width`：浏览器窗口所在的屏幕的宽度（单位像素）。
- `Screen.availHeight`：浏览器窗口可用的屏幕高度（单位像素）。因为部分空间可能不可用，比如系统的任务栏或者 Mac 系统屏幕底部的 Dock 区，这个属性等于`height`减去那些被系统组件的高度。
- `Screen.availWidth`：浏览器窗口可用的屏幕宽度（单位像素）。
- `Screen.pixelDepth`：整数，表示屏幕的色彩位数，比如`24`表示屏幕提供24位色彩。
- `Screen.colorDepth`：`Screen.pixelDepth`的别名。
- `Screen.orientation`：返回一个对象，表示屏幕的方向。该对象的`type`属性是一个字符串，表示屏幕的具体方向，`landscape-primary`表示横放，`landscape-secondary`表示颠倒的横放，`portrait-primary`表示竖放，`portrait-secondary`表示颠倒的竖放。

### history对象

`window.history`属性指向 History 对象，它表示当前窗口的浏览历史。

History 对象保存了当前窗口访问过的所有页面网址。由于安全原因，浏览器不允许脚本读取这些地址，但是允许在地址之间导航。

History 对象主要有两个属性。

- `History.length`：当前窗口访问过的网址数量（包括当前网页）
- `History.state`：History 堆栈最上层的状态值

#### 方法

##### History.back()

移动到上一个网址，等同于点击浏览器的后退键。对于第一个访问的网址，该方法无效果。

##### History.forward()

移动到下一个网址，等同于点击浏览器的前进键。对于最后一个访问的网址，该方法无效果。

##### History.go()

接受一个整数作为参数，以当前网址为基准，移动到参数指定的网址，比如`go(1)`相当于`forward()`，`go(-1)`相当于`back()`。如果参数超过实际存在的网址范围，该方法无效果；如果不指定参数，默认参数为`0`，相当于刷新当前页面。

##### History.pushState()

`History.pushState()`方法用于在历史中添加一条记录。

```
window.history.pushState(state, title, url)
```

该方法接受三个参数，依次为：

- `state`：一个与添加的记录相关联的状态对象，主要用于`popstate`事件。该事件触发时，该对象会传入回调函数。也就是说，浏览器会将这个对象序列化以后保留在本地，重新载入这个页面的时候，可以拿到这个对象。如果不需要这个对象，此处可以填`null`。
- `title`：新页面的标题。但是，现在所有浏览器都忽视这个参数，所以这里可以填空字符串。
- `url`：新的网址，必须与当前页面处在同一个域。浏览器的地址栏将显示这个网址。

`pushState()`方法不会触发页面刷新，只是导致 History 对象发生变化，地址栏会有反应。

使用该方法之后，就可以用`History.state`属性读出状态对象。

如果`pushState`的 URL 参数设置了一个新的锚点值（即`hash`），并不会触发`hashchange`事件。反过来，如果 URL 的锚点值变了，则会在 History 对象创建一条浏览记录。

如果`pushState()`方法设置了一个跨域网址，则会报错。

##### History.replaceState()

`History.replaceState()`方法用来修改 History 对象的当前记录，其他都与`pushState()`方法一模一样。

#### popstate事件

回调函数的参数是一个`event`事件对象，它的`state`属性指向`pushState`和`replaceState`方法为当前 URL 所提供的状态对象

每当同一个文档的浏览历史（即`history`对象）出现变化时，就会触发`popstate`事件。

* 仅调用`pushState()`方法或`replaceState()`方法 ，并不会触发该事件，只有用户点击浏览器倒退按钮和前进按钮，或者使用 JavaScript 调用`History.back()`、`History.forward()`、`History.go()`方法时才会触发。

* 该事件只针对同一个文档，如果浏览历史的切换，导致加载不同的文档，该事件也不会触发。
* 页面第一次加载的时候，浏览器不会触发`popstate`事件。

### Location对象

#### 属性

| 属性     | 说明                        |
| -------- | --------------------------- |
| href     | 整个URL                     |
| protocol | 当前URL协议                 |
| host     | 主机，端口非80、443，则包含 |
| hostname | 主机名，不含端口            |
| port     | 端口名                      |
| pathname | URL的路径部分               |
| search   | 查询字符串，`?`后           |
| hash     | 片段字符串，`#`             |
| username | 域名前用户名                |
| password | 域名前密码                  |
| origin   | 协议、主机名、端口          |

只有`origin`属性是只读的，其他属性都可写。

如果对`Location.href`写入新的 URL 地址，浏览器会立刻跳转到这个新地址。直接改写`location`，相当于写入`href`属性。

#### 方法

##### location.assign()

`assign`方法接受一个 URL 字符串作为参数，使得浏览器立刻跳转到新的 URL。如果参数不是有效的 URL 字符串，则会报错。

##### location.replace()

`replace`方法接受一个 URL 字符串作为参数，使得浏览器立刻跳转到新的 URL。如果参数不是有效的 URL 字符串，则会报错。

它与`assign`方法的差异在于，`replace`会在浏览器的浏览历史`History`里面删除当前网址。

##### location.reload()

`reload`方法使得浏览器重新加载当前网址，相当于按下浏览器的刷新按钮。

规范中reload没有参数，重新加载后视口位置不变，firefox支持一个bool参数，true则强制从服务器加载。

##### location.toString()

`toString`方法返回整个 URL 字符串，相当于读取`Location.href`属性。

## URL

### 编解码

网页的 URL 只能包含合法的字符。合法字符分成两类。

- URL 元字符：分号（`;`），逗号（`,`），斜杠（`/`），问号（`?`），冒号（`:`），at（`@`），`&`，等号（`=`），加号（`+`），美元符号（`$`），井号（`#`）
- 语义字符：`a-z`，`A-Z`，`0-9`，连词号（`-`），下划线（`_`），点（`.`），感叹号（`!`），波浪线（`~`），星号（`*`），单引号（`'`），圆括号（`()`）

除了以上字符，其他字符出现在 URL 之中都必须转义，规则是根据操作系统的默认编码，将每个字节转为百分号（`%`）加上两个大写的十六进制字母。

#### encodeURI()

`encodeURI()`方法用于转码整个 URL。它的参数是一个字符串，代表整个 URL。它会将元字符和语义字符之外的字符，都进行转义。

#### encodeURIComponent()

`encodeURIComponent()`方法用于转码 URL 的组成部分，会转码除了语义字符之外的所有字符，即元字符也会被转码。所以，它不能用于转码整个 URL。它接受一个参数，就是 URL 的片段。

#### decodeURI()

`decodeURI()`方法用于整个 URL 的解码。它是`encodeURI()`方法的逆运算。它接受一个参数，就是转码后的 URL。

#### decodeURIComponent()

`decodeURIComponent()`用于URL 片段的解码。它是`encodeURIComponent()`方法的逆运算。它接受一个参数，就是转码后的 URL 片段。

### 构造函数

`URL()`作为构造函数，可以生成 URL 实例。它接受一个表示 URL 字符串或URL实例作为参数。如果参数不是合法的 URL，会报错。

如果 URL 字符串是一个相对路径，那么需要表示绝对路径的第二个参数，作为计算基准。

**实例属性**

URL实例属性与location相同，多出searchParams属性，该属性返回一个URLSearchParams实例。

### 静态方法

#### URL.createObjectURL()

`URL.createObjectURL()`方法用来为上传/下载的文件、流媒体文件生成一个 URL 字符串。这个字符串代表了`File`对象或`Blob`对象的 URL。

每次使用`URL.createObjectURL()`方法，都会在内存里面生成一个 URL 实例。如果不再需要该方法生成的 URL 字符串，为了节省内存，可以使用`URL.revokeObjectURL()`方法释放这个实例。

#### URL.revokeObjectURL()

`URL.revokeObjectURL()`方法用来释放`URL.createObjectURL()`方法生成的 URL 实例。它的参数就是`URL.createObjectURL()`方法返回的 URL 字符串。

### Data URL

Data URL 是前缀为`data:`协议的 URL； 它允许内容创建者向文档中嵌入小文件，比如图片等。 Data URL 由四部分组成：

- 前缀`data:`
- 指示数据类型的 MIME 类型。例如`image/jpeg`表示 JPEG 图像文件；如果此部分被省略，则默认值为`text/plain;charset=US-SACII`
- 如果为非文本数据，则可选 base64 做标记
- 数据

```text
data:[mediatype][;base63], data
```

### URLSearchParams对象

`URLSearchParams`对象是浏览器的原生对象，用来构造、解析和处理 URL 的查询字符串（即 URL 问号后面的部分）。

它本身也是一个构造函数，可以生成实例。参数可以为查询字符串，起首的问号`?`有没有都行，也可以是对应查询字符串的数组或对象。

`URLSearchParams`会对查询字符串自动编码。

`URLSearchParams`没有实例属性，只有实例方法。

#### toString()

`toString`方法返回实例的字符串形式。

#### append()

`append()`方法用来追加一个查询参数,不会识别是否键名已经存在。它接受两个参数，第一个为键名，第二个为键值，没有返回值。

#### delete()

`delete()`方法用来删除所有指定的查询参数。它接受键名作为参数。

#### has()

`has()`方法返回一个布尔值，表示查询字符串是否包含指定的键名。

#### set()

`set()`方法用来设置查询字符串的键值。

它接受两个参数，第一个是键名，第二个是键值。如果是已经存在的键，键值会被改写，否则会被追加。

如果有多个的同名键，`set`会移除现存所有的键。

#### get()

`get()`方法用来读取查询字符串里面的指定键。它接受键名作为参数。

* 返回的是字符串，如果原始值是数值，需要转一下类型
* 如果指定的键名不存在，返回值是`null`。
* 如果有多个的同名键，`get`返回位置最前面的那个键值。

#### getAll()

`getAll()`方法返回一个数组，成员是指定键的所有键值。它接受键名作为参数。

#### sort()

`sort()`方法对查询字符串里面的键进行排序，规则是按照 Unicode 码点从小到大排列。

如果有两个同名的键，它们之间不会排序，而是保留原始的顺序。

#### keys(), values(), entries()

这三个方法都返回一个遍历器对象，供`for...of`循环遍历。它们的区别在于，`keys`方法返回的是键名的遍历器，`values`方法返回的是键值的遍历器，`entries`返回的是键值对的遍历器。

## Navigator对象

### 属性

| 属性          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| userAgent     | 表示用户设备信息，包含了浏览器的厂商、版本、操作系统等信息。 |
| plugins       | 返回一个类似数组的对象，成员是 Plugin 实例对象，表示浏览器安装的插件 |
| platform      | 返回用户的操作系统信息                                       |
| onLine        | 返回一个布尔值，表示用户当前在线还是离线                     |
| language      | 返回字符串，表示浏览器的首选语言                             |
| languages     | 返回一个数组，表示用户可以接受的语言。                       |
| geolocation   | 返回一个 Geolocation 对象，包含用户地理位置的信息            |
| cookieEnabled | 返回一个布尔值，表示浏览器的 Cookie 功能是否打开             |
| clipboard     | 返回clipboard对象                                            |
| premissions   | 返回permissions对象                                          |

#### onLine

浏览器可以连接局域网，但是局域网不能连通外网。这时，有的浏览器的`onLine`属性会返回`true`，所以不能假定只要是`true`，用户就一定能访问互联网。

用户变成在线会触发`online`事件，变成离线会触发`offline`事件，可以通过`window.ononline`和`window.onoffline`指定这两个事件的回调函数。

#### language, languages

HTTP 请求头信息的`Accept-Language`字段，就来自languages。

如果这两个属性发生变化，就会在`window`对象上触发`languagechange`事件。

#### geoloaction

该 API 只有在 HTTPS 协议下可用，否则调用下面方法时会报错。

Geolocation 对象提供下面三个方法。

- Geolocation.getCurrentPosition()：得到用户的当前位置
- Geolocation.watchPosition()：监听用户位置变化
- Geolocation.clearWatch()：取消`watchPosition()`方法指定的监听函数

注意，调用这三个方法时，浏览器会跳出一个对话框，要求用户给予授权。

#### 实验属性

Navigator 对象有一些实验性属性，在部分浏览器可用。

##### deviceMemory

`navigator.deviceMemory`属性返回当前计算机的内存数量（单位为 GB）。该属性只读，只在 HTTPS 环境下可用。

它的返回值是一个近似值，四舍五入到最接近的2的幂，通常是 0.25、0.5、1、2、4、8。实际内存超过 8GB，也返回`8`。

##### hardwareConcurrency

`navigator.hardwareConcurrency`属性返回用户计算机上可用的逻辑处理器的数量。该属性只读。

##### connection

`navigator.connection`属性返回一个对象，包含当前网络连接的相关信息。

- downlink：有效带宽估计值（单位：兆比特/秒，Mbps），四舍五入到每秒 25KB 的最接近倍数。
- downlinkMax：当前连接的最大下行链路速度（单位：兆比特每秒，Mbps）。
- effectiveType：返回连接的等效类型，可能的值为`slow-2g`、`2g`、`3g`、`4g`。
- rtt：当前连接的估计有效往返时间，四舍五入到最接近的25毫秒的倍数。
- saveData：用户是否设置了浏览器的减少数据使用量选项（比如不加载图片），返回`true`或者`false`。
- type：当前连接的介质类型，可能的值为`bluetooth`、`cellular`、`ethernet`、`none`、`wifi`、`wimax`、`other`、`unknown`。

### 方法

#### sendBeacon()

用于向服务器异步发送数据。

#### javaEnabled()

返回一个布尔值，表示浏览器是否能运行 Java Applet 小程序。

### clipboard

clipboard 实现了 Clipboard API，在用户授予权限的情况下可以提供系统剪切板的读写访问能力。

#### 方法

clipboard 读写方法都返回Promise对象。clipboard方法有：

* read()
* readText()
* write()
* writeText()

#### 基本使用

##### 读取剪切板内容

```js
// 是否能够有读取剪贴板的权限
// result.state == "granted" || result.state == "prompt"
const result = await navigator.permissions.query({ name: "clipboard-read" });
const hasFocus = document.hasFocus();
if (hasFocus && (result.state == 'granted' || result.state == 'prompt')){
    // 获取剪贴板内容
	const text = await navigator.clipboard.readText();
}
```



## 二进制

### Blob对象

Blob 对象表示一个二进制文件的数据内容，比如一个图片文件的内容就可以通过 Blob 对象读写。

#### 构造函数

`Blob`构造函数接受两个参数。第一个参数是数组，成员是字符串或二进制对象，表示新生成的`Blob`实例对象的内容；第二个参数是可选的，是一个配置对象，目前只有一个属性`type`，它的值是一个字符串，表示数据的 MIME 类型，默认是空字符串。

#### 实例属性和方法

`Blob`具有两个实例属性`size`和`type`，分别返回数据的大小和类型。

##### slice()

实例方法`slice`，用来拷贝原来的数据，返回的也是一个`Blob`实例。

`slice`方法有三个参数，都是可选的。它们依次是起始的字节位置（默认为0）、结束的字节位置（默认为`size`属性的值，该位置本身将不包含在拷贝的数据之中）、新实例的数据类型（默认为空字符串）。

#### 应用

##### 获取文件信息

文件选择器`<input type="file">`用来让用户选取文件。文件选择器返回一个 FileList 对象，该对象是一个类似数组的成员，每个成员都是一个 File 实例对象。File 实例对象是一个特殊的 Blob 实例，增加了`name`和`lastModifiedDate`属性。

##### 下载文件

AJAX 请求时，如果指定`responseType`属性为`blob`，下载下来的就是一个 Blob 对象。

##### 生成URL

浏览器允许使用`URL.createObjectURL()`方法，针对 Blob 对象生成一个临时 URL，以便于某些 API 使用。这个 URL 以`blob://`开头，表明对应一个 Blob 对象，协议头后面是一个识别符，用来唯一对应内存里面的 Blob 对象。

##### 读取文件

取得 Blob 对象以后，可以通过`FileReader`对象，读取 Blob 对象的内容，即文件内容。

FileReader 对象提供四个方法，处理 Blob 对象。Blob 对象作为参数传入这些方法，然后以指定的格式返回。

### File对象

File 对象代表一个文件，用来读写文件信息。它继承了 Blob 对象。

#### 构造函数

`File()`构造函数接受三个参数。

- array：一个数组，成员可以是二进制对象或字符串，表示文件的内容。
- name：字符串，表示文件名或文件路径。
- options：配置对象，设置实例的属性。该参数可选。

第三个参数配置对象，可以设置两个属性。

- type：字符串，表示实例对象的 MIME 类型，默认值为空字符串。
- lastModified：时间戳，表示上次修改的时间，默认为`Date.now()`。

#### FileList对象

`FileList`对象是一个类似数组的对象，代表一组选中的文件，每个成员都是一个 File 实例。它主要出现在两个场合。

- 文件控件节点（`<input type="file">`）的`files`属性，返回一个 FileList 实例。
- 拖拉一组文件时，目标区的`DataTransfer.files`属性，返回一个 FileList 实例。

FileList 的实例方法主要是`item()`，用来返回指定位置的实例。它接受一个整数作为参数，表示位置的序号（从零开始）。但是，由于 FileList 的实例是一个类似数组的对象，可以直接用方括号运算符。

### FileReader对象

FileReader 对象用于读取 File 对象或 Blob 对象所包含的文件内容。

#### 实例属性

- FileReader.error：读取文件时产生的错误对象
- FileReader.readyState：整数，表示读取文件时的当前状态。一共有三种可能的状态，`0`表示尚未加载任何数据，`1`表示数据正在加载，`2`表示加载完成。
- FileReader.result：读取完成后的文件内容，有可能是字符串，也可能是一个 ArrayBuffer 实例。

同时FileReader对象还有`abort`、`error`、`load`、`loadstart`、`loadend`、`progress`事件。

#### 实例方法

##### abort()

终止读取操作，`readyState`属性将变成`2`。

##### readAsArrayBuffer()

以 ArrayBuffer 的格式读取文件，读取完成后`result`属性将返回一个 ArrayBuffer 实例。

##### readAsBinaryString()

读取完成后，`result`属性将返回原始的二进制字符串。

##### readAsDataURL()

读取完成后，`result`属性将返回一个 Data URL 格式（Base64 编码）的字符串，代表文件内容。对于图片文件，这个字符串可以用于`<img>`元素的`src`属性。

这个字符串不能直接进行 Base64 解码，必须把前缀`data:*/*;base64,`从字符串里删除以后，再进行解码。

##### readAsText()

读取完成后，`result`属性将返回文件内容的文本字符串。该方法的第一个参数是代表文件的 Blob 实例，第二个参数是可选的，表示文本编码，默认为 UTF-8。

## FormData对象

表单数据以键值对的形式向服务器发送，这个过程是浏览器自动完成的。但是有时候，我们希望通过脚本完成这个过程，构造或编辑表单的键值对，然后通过脚本发送给服务器。浏览器原生提供了 FormData 对象来完成这项工作。

### 构造函数

`FormData()`首先是一个构造函数，用来生成表单的实例。

`FormData()`构造函数的参数是一个 DOM 的表单元素，构造函数会自动处理表单的键值对。这个参数是可选的，如果省略该参数，就表示一个空的表单。

### 实例方法

#### get(), getAll()

`get()`获取指定键名对应的键值，参数为键名。如果有多个同名的键值对，则返回第一个键值对的键值。

`getAll()`返回一个数组，表示指定键名对应的所有键值。

#### set()

设置指定键名的键值，参数为键名。如果键名不存在，会添加这个键值对，否则会更新指定键名的键值。如果第二个参数是文件，还可以使用第三个参数，表示文件名。

#### delete()

删除指定键名的键值对。

#### append()

添加一个键值对。如果键名重复，则会生成两个相同键名的键值对。如果第二个参数是文件，还可以使用第三个参数，表示文件名。

#### has()

表示是否具有该键名的键值对。

#### keys(), values(), entries()

分别表示返回一个遍历器对象，用于`for...of`循环遍历所有的键名、键值、键值对。

## WebWorker

Web Worker (工作线程) 是 HTML5 中提出的概念，分为两种类型，专用线程（Dedicated Web Worker） 和共享线程（Shared Web Worker）。专用线程仅能被创建它的脚本所使用（一个专用线程对应一个主线程），而共享线程能够在不同的脚本中使用（一个共享线程对应多个主线程）。

Web Worker 的作用，就是为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行，利用多核CPU的性能。

**特点**

1. 同源限制
2. DOM限制
3. 全局对象限制
4. 通信限制
5. 脚本限制
6. 文件限制

### 基本用法

#### 主线程

主线程使用Worker构造函数传入一个网络脚本文件，返回Woker线程对象。

`Worker()`构造函数，可以接受两个参数。第一个参数是脚本的网址（必须遵守同源政策），该参数是必需的，且只能加载 JS 脚本，否则会报错。第二个参数是配置对象，该对象可选，可以指定 `type`、`credentials`、`name` 三个属性。

通过URL.createObjectURL()创建URL对象，可以实现创建内嵌的worker。

Worker 线程对象的属性和方法如下。

- Worker.onerror：指定 error 事件的监听函数。

  错误事件有三个实用的属性：filename - 发生错误的脚本文件名；lineno - 出现错误的行号；以及 message - 可读性良好的错误消息。

- Worker.onmessage：指定 message 事件的监听函数，发送过来的数据在`Event.data`属性中。

- Worker.onmessageerror：指定 messageerror 事件的监听函数。发送的数据无法序列化成字符串时，会触发这个事件。

- Worker.postMessage()：向 Worker 线程发送消息。

- Worker.terminate()：立即终止 Worker 线程

#### Worker线程

Worker线程有自身的全局对象，内部可以使用`onmessage`、`[self.][this.] addEventListener()`能够指定监听函数，监听函数的参数是事件对象，事件对象有个data属性，可以获取主线程发送的数据。

Worker 线程有一些自己的全局属性和方法。

- self.name： Worker 的名字。该属性只读，由构造函数指定。
- self.onmessage：指定`message`事件的监听函数。
- self.onmessageerror：指定 messageerror 事件的监听函数。发送的数据无法序列化成字符串时，会触发这个事件。
- self.close()：关闭 Worker 线程。
- self.postMessage()：向产生这个 Worker 的线程发送消息。
- self.importScripts()：加载 JS 脚本，该方法可以接收多个脚本作为参数。

#### SharedWorker

SharedWorker 提供了能够让不同标签中页面共享的同一个 Worker 脚本线程的能力。

```js
// main.js
var myWorker = new SharedWorker("worker.js");
myWorker.port.start();
myWorker.port.postMessage("hello, I'm main");
myWorker.port.onmessage = function(e) {
  console.log('Message received from worker');
}
```

可以在多个同源页面创建SharedWorker实例。

```js
// worker.js
onconnect = function(e) {
  var port = e.ports[0];
  port.addEventListener('message', function(e) {
    var workerResult = 'Result: ' + (e.data[0] * e.data[1]);
    port.postMessage(workerResult);
  });
  port.start();
}
```

### 数据通信

主线程与 Worker 之间的通信内容，可以是文本，也可以是对象。需要注意的是，这种通信是拷贝关系，即是传值而不是传址，Worker 对通信内容的修改，不会影响到主线程。

#### Transferable Objects

如果传输很大的二进制数据，采用拷贝的方式会造成性能问题。为了解决这个问题，JavaScript 允许主线程把二进制数据直接转移给子线程，但是一旦转移，主线程就无法再使用这些二进制数据了，这种转移数据的方法，叫做[Transferable Objects](https://www.w3.org/html/wg/drafts/html/master/infrastructure.html#transferable-objects)。

```javascript
// Transferable Objects 格式
worker.postMessage(arrayBuffer, [arrayBuffer]);

// 例子
var ab = new ArrayBuffer(1);
worker.postMessage(ab, [ab]);
```

