# 盒模型

所有HTML标签都可以看成矩形盒子，由width、height、padding、 border构成，称为“盒模型"。

## 基本概念

### width和height属性

#### width

* width属性表示盒子内容的宽度
* width属性的单位通常是px，移动端开发也会涉及百分数、rem等单位
* 当块级元素(div、h系列、li等）没有设置width属性时，它将自动撑满，但这并不意味着width可以继承

#### height

* height属性表示盒子内容的高度
* height属性的单位通常是px，移动端开发也会涉及百分数、rem等单位
* 盒子的height属性如果不设置，它将自动被其内容撑开，如果没有内容，则height默认是0

### padding属性

padding是盒子的内边距，即盒子边框内壁到文字的距离

padding四个方向可以通过其子属性进行设置。子属性可以用于层叠父属性，用来单独设置。

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

margin四个方向可以通过其子属性进行设置。

| 子属性        | 说明     |
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

* 垂直居中

  盒子的垂直居中，需要使用绝对定位技术实现

* margin传递问题

  当子元素设置margin-top，父元素不设置，那么父元素默认会有与子元素相同的margin-top，这个现象叫做margin传递。

  解决方式

  1. 使用padding替换margin，在父元素中使用padding-top
  
     > 由于padding会增大元素的尺寸，所以使用该方式后，要注意元素的尺寸问题。
  
  2. 给父元素设置overflow: hidden;
  
     > overflow: hidden;的意思是"超出隐藏'，如果子元素必须超出父元素才能实现效果，此时不建议使用该属性，否则子元素超出的部分可能会被隐藏掉。
  
  3. 父容器加上边框
  
  4. 子元素设置浮动
  
  5. 父元素设置浮动
  
  6. 子元素设置absolute定位

### 盒模型计算

盒子的总宽度= width +左右padding +左右border

盒子的总高度= height + 上下padding + 上下border

子盒子实际最多只能占有父盒子的width和height空间。

元素的空间尺寸需要加上margin值。

### box-sizing属性

将盒子添加了box-sizing: border-box;之后，盒子的width、height数字就表示盒子实际占有的宽高(不含margin)了，即padding、 border变为“内缩”的，不再“外扩”。

box-sizing属性大量应用于移动网页制作中，因为它结合百分比布局、弹性布局等非常好用，在PC页面开发中使用较少。

## 行内元素和块级元素

### display属性

| 属性类型 | 并排显示？ | 设置宽高？ | 不设置width   | 举例                                  |
| -------- | ---------- | ---------- | ------------- | ------------------------------------- |
| 块级元素 | N          | Y          | width自动撑满 | div、section、header、h系列、li、ul等 |
| 行内元素 | Y          | N          | width自动收缩 | a、span、em、b、u、i等                |
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









