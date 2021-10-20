# 盒模型

所有HTML标签都可以看成矩形盒子，称为“盒模型"。盒模型的宽高，即offsetWidth、offsetHeight，默认由width、height、padding、 border构成。

## 基本概念

### width和height属性

**width**

* width属性表示盒子内容的宽度
* width属性的单位通常是px，移动端开发也会涉及百分数、rem等单位
* 当块级元素(div、h系列、li等）没有设置width属性时，它将自动撑满，但这并不意味着width可以继承

**height**

* height属性表示盒子内容的高度
* height属性的单位通常是px，移动端开发也会涉及百分数、rem等单位
* 盒子的height属性如果不设置，它将自动被其内容撑开，如果没有内容，则height默认是0

### padding属性

padding是盒子的内边距，即盒子边框内壁到文字的距离

padding四个方向可以通过其小属性进行设置。子属性可以用于层叠父属性，用来单独设置。

| 子属性         | 说明      |
| -------------- | --------- |
| padding-top    | 上padding |
| padding-right  | 右padding |
| padding-bottom | 下padding |
| padding-left   | 左padding |

> padding属性如果用四个数值以空格隔开进行设置，分别表示上、右、下、左的padding。
>
> padding属性如果用三个数值以空格隔开进行设置，分别表示上、左右、下的padding。
>
> padding属性如果用二个数值以空格隔开进行设置，分别表示上下、左右的padding

### margin属性

margin是盒子的外边距，即盒子和其他盒子之间的距离

margin四个方向可以通过其小属性进行设置。

| 小属性        | 说明     |
| ------------- | -------- |
| margin-top    | 上margin |
| margin-right  | 右margin |
| margin-bottom | 下margin |
| margin-left   | 左margin |

> 竖直方向的margin有塌陷现象:小的margin会塌陷到大的margin中，从而margin不叠加，只以大值为准

* 样式清除

  一些元素（比如body、ul、p等）都有默认的margin，在开始制作网页的时候，要将他们清除。
  
  可以使用通配符选择器(*)清除样式，然而通配符有效率问题应该使用并集选择器。

    ```css
    *{
    margin:0;
    padding:0;
    }
    ```

* 水平居中

  将盒子左右两边的margin都设置为auto，盒子将水平居中


### 盒模型计算

盒子的总宽度= width +左右padding +左右border

盒子的总高度= height + 上下padding + 上下border

子盒子实际最多只能占有父盒子的width和height空间。

元素的空间尺寸需要加上margin值。

### 盒模型的行为

在CSS中可以使用box-sizing属性调整盒模型的行为。

box-sizing的默认值为content-box，这意味任何指定的宽或高都只会设置盒子内容的大小。将box-sizing设置为border-box后，height和width属性会设置内容、内边距以及边框的大小总和。

box-sizing属性大量应用于移动网页制作中，因为它结合百分比布局、弹性布局等非常好用，在PC页面开发中使用较少。

#### 全局设置border-box

使用通用选择器、两个伪元素选择器能够选择页面所有元素，将它们box-sizing设置为border-box能够达到全局设置border-box的目的。但是如果使用第三方组件，则盒模型可能会存在问题，更好的解决的办法是利用继承修改盒模型。这样只需要选中第三方组件的顶级容器，将其恢复为content-box即可。

```css
:root{
    box-sizing: border-box;
}
*,
::before,
::after{
    box-sizing: inherit;
}
```

## 元素高度

普通文档流是为限定的宽度和无限的高度设计的。内容会填满视口的宽度，然后在必要的时候折行。因此，容器的高度由内容天然地决定，而不是容器自己决定。

> 普通文档流指的是网页元素的默认布局行为。

min-height和max-height可以用来指定元素高度最小或最大值。

### 控制溢出

当明确设置一个元素的高度时，内容在元素内放不下，渲染到父元素外面时，就会发生溢出现象。

#### overflow

如果盒内元素宽高超出盒子本身宽高，则可以通过设置overflow值来决定显示效果。

| 属性值  | 说明                                         |
| ------- | -------------------------------------------- |
| visible | 所有内容都显示                               |
| hidden  | 溢出容器内边距边缘的内容被隐藏               |
| scroll  | 容器出现滚动条，用户可以通过滚动查看剩余内容 |
| auto    | 根据尺寸来决定xy轴是否出现滚动条             |
| inherit | 兼容性问题                                   |

**小属性**

可以通过小属性overflow-x、overflow-y单独控制水平或垂直方向的溢出。

如果overflow-x和overflow-y值相同，则等同于overflow。如果overflow-x和overflow-y值不同，且其中一个属性的值被赋予visible，而另外一个被赋予hidden、scroll、auto，那么这个visible会被重置为auto。

**注意事项**

* 宽度设定兼容性：

  ```html
  <style>
      .box{width:400px;height:100px;overflow:auto;}
      .content{width:100%;height:200px;bacground-color:red;}
  </style>
  <div class="box">
     <div class="content">
      </div>
  </div>
  ```

  在IE7下会出现水平滚动条，IE8则不会，因为IE7将100%宽度算成400px，而垂直滚动条本身占据一定宽度。可以删除100%，去掉水平滚动条。

* 作用前提

  1. 不是display:inline
  2. 对应方位尺寸限制，width、height、max-width、max-height、absolute拉伸
  3. 对于单元格td，还需要设置table为table-layout：fixed

* visible

  IE7浏览器下，文字越多，按钮两侧padding留白就越大，可以给按钮添加overflow：visible消除这个特性。

#### overflow与absolute

当设置overflow属性为非visible的容器内子元素是绝对定位时，overflow可能失效。

失效原因：绝对定位元素不总是被父级overflow属性剪裁，尤其当overflow在绝对定位元素及其包含块之间的时候。

> 包含块：含position:relative/absolute/fixed声明的父级元素，没有则body元素

**避免失效方式**

1. overflow元素自身为包含块
2. overflow元素子元素为包含块
3. 任意合法transform声明当作包含块
   * overflow元素自身transform IE9+/FireFox
   * overflow元素子元素transform Chrome/Safari/Opera/FireFox/IE9+

#### resize

resize可以拉伸元素尺寸，该元素作用前提是overflow属性不能是visible。

| 属性值     | 说明     |
| ---------- | -------- |
| both       | 水平垂直 |
| horizontal | 只能水平 |
| vertical   | 只能垂直 |

resize拖拽区域大小为17px*17px

### 百分比高度

如果父元素没有设置高度，子元素高度使用百分比声明，那么浏览器会忽略该声明。因为百分比参考的是元素容器块的大小，但是容器的高度通常是由子元素的高度决定的。

#### 等高列

等高列是指多个列高度相等。任意一列内容增加，两列的高度增加，底部对齐。

可以借助css表格和flexbox实现等高列。

## margin问题

### 负外边距

margin可以设置为负值，这称为负外边距。负外边距的具体行为取决于设置在元素的哪边，如果设置左边或顶部的负外边距，元素就会相应地向左或向上移动，导致元素与它前面的元素重叠，如果设置右边或者底部的负外边距，并不会移动元素，而是将它后面的元素拉过来，相当于元素自身的宽度减小。

### 外边距折叠

当一个元素的底部和另一元素的顶部的外边距相邻时，这两个外边距就会重叠，形成一个外边距。这种现象被称作外边距折叠。折叠后形成的外边距大小等于相邻外边距中的最大值。

#### 多个外边距折叠

即使两个元素不是相邻的兄弟节点也会产生外边距折叠。在没有其他CSS的影响下，所有相邻的顶部和底部外边距都会折叠，即使是同一个元素。例如，在页面添加空的、无样式div，它自己的顶部和底部外边距会折叠。

#### 防止外边距折叠方法

* 对元素添加边框或内边距
* 对元素使用overflow非auto值
* 元素为浮动、内联块、绝对定位、固定定位
* flexbox布局、网格布局

## 行内元素和块级元素

### display属性

| 属性类型 | 设置宽高？ | 并排显示？ | 不设置width   | 举例                                  |
| -------- | ---------- | ---------- | ------------- | ------------------------------------- |
| 块级元素 | Y          | N          | width自动撑满 | div、section、header、h系列、li、ul等 |
| 行内元素 | N          | Y          | width自动收缩 | a、span、em、b、u、i等                |
| 行内块   | Y          | Y          | width自动收缩 | img、表单元素                         |

> 行内元素设置宽高无效，如果设置padding，文字本身位置不会改变，其外围改变，会侵入到其他元素的位置。

### 元素的隐藏

* display: none;

  可以将元素隐藏，元素将彻底放弃位置，如同没有写它的标签─样

* visibility: hidden;

  可以也可以将元素隐藏，但是元素不放弃自己的位置

### 行内元素和块级元素的转换

设置display属性值可以对行内元素和块级元素进行转换。

| 属性值       | 说明                                               |
| ------------ | -------------------------------------------------- |
| block        | 将元素转为块级元素                                 |
| inline       | 将元素转为行内元素，将元素转为行内元素的应用不多见 |
| inline-block | 将元素转为行内块                                   |

## **滚动条**

出现的条件

1. overflow：auto/overflow：scroll
2. 本身自带滚动条的元素，textarea、html

### html与滚动条

无论什么浏览器，默认滚动条均来自`<html>`而不是`<body>`标签。

> IE7-浏览器默认∶html { overflow-y: scroll;}
>
> IE8＋等浏览器默认:html { overflow: auto; }

* 去除滚动条

  如果想去除页面默认滚动条，只需要设置html的overflow为hidden。

* JS获取滚动高度

  Chrome浏览器是:document.body. scrollTop ;其他浏览器是∶
  document.documentElement.scrollTop ;

    ```javascript
  var st = document.documentElement.scrollTop||document.body.scrollTop;
    ```

### 滚动条问题

#### padding-bottom缺失现象

如果容器内元素设置padding-bottom，除了Chrome浏览器会显示padding-bottom，其余所有浏览器padding-bottom将缺失。这将导致不一样的scrollHeight（元素内容高度）。

#### 宽度机制

滚动条会占用容器的可用宽度或高度。而这可能会给原本布局造成问题。

可以根据容器宽度减去内部元素宽度，算出滚动条宽度，均是17px。

水平居中跳动问题修复

1. html{overflow-y:scorll;}

2. .container{padding-left:calc(100vw-100%)}IE9+浏览器

   > 100vw-浏览器宽度；100%-可用内容宽度

### 自定义滚动条-webkit

| 属性                            | 说明     |
| ------------------------------- | -------- |
| ::-webkit-scrollbar             | 整体部分 |
| ::-webkit-scrollbar-button      | 两端按钮 |
| ::-webkit-scrollbar-track       | 外层轨道 |
| ::-webkit-scrollbar-track-piece | 内层轨道 |
| ::-webkit-scrollbar-thumb       | 滚动滑块 |
| ::-webkit-scrollbar-corner      | 边角     |

* 实际开发示例

  ```css
  ::-webkit-scrollbar { /*血槽宽度*/
  width: 8px; height: 8px;
  }
  ::-webkit-scrollbar-thumb {/*拖动条*/
  background-color: rgba(o,o,0,.3);border-radius: 6px;
  }
  ::-webkit-scrollbar-track {/*背景槽*/
  background-color: #ddd;
  border-radius: 6px;
  }
  ```

* ios原生滚动回调效果

  ```css
  -webkit-overflow-scrolling:touch;
  ```


