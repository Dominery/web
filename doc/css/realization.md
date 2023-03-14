# 效果实现

## 功能

### 垂直居中

在容器里让内容垂直居中的最好方式是根据特定场景考虑不同因素。

* 自然高度的容器

  给容器加上相等的上下内边距让内容居中。

* 容器高度指定或避免使用内边距

  ```css
  .container{
      display: table-cell;
      vertical-align: middle;
  }
  ```

* 可以使用Flexbox

* 容器里面内容只有一行文字

  设置行高等于容器高度，如果内容不是行内元素，设置元素的display为inline-block。

* 容器高度明确

  * 内容高度明确

    绝对定位加margin-top负值

  * 自然高度的内容

    内容设置绝对定位，设置translate

### 文字溢出隐藏

**单行文字溢出隐藏**

```css
.text-ellipsis{
    overflow:hidden;
    text-overflow:ellipsis;
    white-space:nowrap;
}
```

flex布局不能和单行文字溢出一起使用。

**多行文字溢出隐藏**

多行文字溢出隐藏兼容性差，只能兼容webkit内核。

```css
.multiline-ellipsis{
    overflow:hidden;
    text-overflow:ellipsis;
    display:-webkit-box;
    -webkit-line-clamp:3;
    -webkit-box-orient:vertical;
    white-space:normal !important;
    word-wrap:break-word;
}
```

多行文字溢出不要与height一起使用。

### 隐藏元素

隐藏元素分为：移动元素、隐藏颜色、缩小尺寸、后置图层、控制可见、移除。

**opacity、filter: opacity()**

设置元素的透明度。影响组合渲染，支持动画，会触发事件。

**transform**

可以使用scale(0)或translate(-9999px, 0)隐藏元素。影响组合渲染，支持动画，无法触发事件。

**clip-path**

`clip-path`属性可以创建一个剪辑区域，区域内元素可见。使用`clip-path: circle(0)`隐藏元素。影响绘制渲染，支持动画，无法触发事件。

**visibility: hidden**

影响组合渲染，不支持动画，无法触发事件。

**display: none**

被隐藏元素不会在页面中占据位置，也不会响应事件。影响布局，不支持动画，无法触发事件。

**z-index**

元素使用定位，并将z-index设置为负值，可以实现隐藏。影响组合渲染，支持动画，无法触发事件。

**position**

元素使用绝对定位，移出屏幕。影响布局，支持动画，支持事件。

**元素覆盖**

通过与背景色相同的伪元素覆盖元素。

**缩小尺寸**

使用width、height，应用overflow: hidden将元素宽高设置为0.

## 技巧

### 毛玻璃特效

```css
.class{
    backdrop-filter: blur(5px);
}
```

`backdrop-filter`属性可以为一个元素后面区域添加图形效果，需要该元素本身部分透明。

### 自适应正方形

```css
.square {
    width: 40%;
    aspect-ratio: 1/1;
}
```

通过`aspect-ratio`可以设置纵横比。

### 绘制三角形

#### border

如果元素的宽高都为0，border会相交，通过该交点可以绘制三角形，设定不同的border值可以绘制出不同角度的三角形。

#### linear-gradient

线性渐变可以创建很多形状，但需要调整渐变的角度。

```css
.triangle {
    width: 160px;
    height: 200px;
    background-repeat: no-repeat;
    background-image: linear-gradient(32deg, red 50%, rgba(255,255,255,0) 50%), linear-gradient(148deg, red 50%, rgba(255,255,255,0) 50%);
    background-size: 100% 50%;
    background-position: top left, bottom left;
}
```

#### clip-path

clip-path属性可以控制图像在指定区域内显示，可能存在兼容性问题。

```css
。triangle {
    width: 80px;
    height: 100px;
    background-color: skyblue;
    clip-path: polygon(0 0, 0 100%, 50% 100%);
}
```

### 九宫格实现

#### Flex

```css
ul {
    display: flex;
    flex-wrap: wrap;
    width: 100%;
}

li {
    width: 30%;
    aspect-ratio: 1/1;
    margin-right: 5%;
    margin-bottom: 5%;
}

li: nth-of-type(3n) {
    margin-right:0;
}

li: nth-of-type(n+7) {
    margin-bottom: 0;
}
```

#### Grid

```css
ul {
    width: 100%;
    height: 100%;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);
    gap: 5%;
}
```

#### Float

```css
ul {
    overflow: hidden;
    width: 100%;
    aspect-ratio: 1/1;
}

li {
    float: left;
    width: 30%;
	height: 30%;
    margin-right: 5%;
    margin-bottom: 5%;
}

li: nth-of-type(3n) {
    margin-right:0;
}

li: nth-of-type(n+7) {
    margin-bottom: 0;
}
```

#### inline-block

inline-block实现方式与float基本一致，元素间可能会出现多余空白，这是字符间距导致，设置`font-size: 0`或者`letter-spacing: -10px`可以解决。

#### table

```css
.table {
    width: 100%;
    aspect-ratio: 1/1;
    display: table;
    border-spacing: 10px;
}
li {
    display: table-row;
}
div {
    width: 30%;
    height: 30%;
    display: table-cell;
    text-align: center;
}
```

