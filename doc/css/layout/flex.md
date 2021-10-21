# Flexbox

Flex，全称弹性盒子布局（Flexible Box Layout），是一种新的布局方式。Flex能够轻松解决等高列和垂直居中的问题。

## 原则

如果元素中display属性值设置为flex或inline-flex，那么称该元素采用了flex布局，该元素成为了一个弹性容器(flex container)。弹性容器中的直接子元素成为弹性子元素(flext item)，称为flex项目。

弹性容器像块元素一样填满可用宽度，但是弹性子元素不一定填满其弹性容器的宽度。弹性子元素默认是在同一行按照从左到右的顺序并排排列。弹性子元素高度相等，该高度由它们的内容决定。

一个弹性容器能控制flex项目的布局。

flex容器默认存在两根轴：主轴和副轴。

主轴的方向为主起点（左）到主终点（右）；副轴垂直主轴，方向从副起点（上）到副终点（下）。

项目默认沿主轴排列，单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫`cross size`。

Flexbox允许使用margin: auto来填充弹性子元素之间的可用空间。Flexbox还允许将最后的菜单项移动到右侧。

## flex容器的属性

display属性值可以是flex和inline-flex。这两者的区别是inline-flex的宽度取决于flex项目。

> flex作用是将对象元素作为弹性盒子显示
>
> inline-flex作用是将对象作为内联块弹性盒子显示

### 弹性方向

Flexbox能够切换主副轴方向，用弹性容器的flex-direction属性控制。该属性初始值为row控制子元素按从左到右排列。

| 属性值         | 作用                       |
| -------------- | -------------------------- |
| row            | 主轴为水平方向，主起点在左 |
| row-reverse    | 主轴为水平方向，主起点在右 |
| column         | 主轴为垂直方法，主起点在上 |
| column-reverse | 主轴为垂直方向，主起点在下 |

### 换行

默认情况，项目都排在主轴线上。flex-wrap属性允许flex项目换到新的一行或多行显示。如果弹性容器使用换行，那么flex项目不会根据flex-shrink值收缩，任何超过弹性容器的子元素都会换行显示。

| 属性值       | 作用             |
| ------------ | ---------------- |
| nowrap       | 不换行           |
| wrap         | 换行，新行在下面 |
| wrap-reverse | 换行，新行在上面 |

> 不换行时，为了能在主轴线上显示所有的flex项目，flex项目会被压缩，即使flex项目设置了宽度。

flex-flew是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。

### 对齐方式

#### justify-content

justify-content属性定义了项目在主轴上的对齐方式。当justify-content为space-between或space-around时，justify-content属性控制子元素沿主轴方向的间距。

间距是在元素的外边距之后进行计算的，而且flex-grow的值要考虑进来。也就是说，如果任意子元素的flex-grow的值不为0，或者任意子元素在主轴方向的外边距值为auto, justify-content就失效了。

| 属性值        | 作用                                                   |
| ------------- | ------------------------------------------------------ |
| flex-start    | 在主起点对齐                                           |
| flex-end      | 在主终点对齐                                           |
| center        | 居中                                                   |
| space-between | 两端对齐，项目间距相等                                 |
| space-around  | 两个项目两侧的间距相等，项目间距比项目与边框间距大一倍 |

#### align-items

align-items定义项目在副轴上如何对齐，默认为stretch。

| 属性值     | 作用                                       |
| ---------- | ------------------------------------------ |
| flex-start | 副起点对齐                                 |
| flex-end   | 副终点对齐                                 |
| center     | flex项目在副轴垂直居中                     |
| baseline   | 项目第一行文字基线对齐                     |
| stretch    | 如果项目未设置高度或设置auto，占满容器高度 |

#### align-content

如果弹性容器设置了换行，align-content属性会控制flex项目在副轴上的间距。属性值与align-items的属性值一样，表现形式与之类似。

## 项目属性

### flex

flex属性是三个不同大小属性的简写：flex-grow、flex-shrink、flex-basis。默认值为0 1 auto。如果属性值为auto，则表示1 1 auto；属性值为none，表示0 0 auto。flex属性控制弹性子元素在主轴方向上的大小

#### flex-basis

flex-basis定义了元素大小的基准值，即一个初始的“主尺寸”。

flex-basis属性可以设置为任意的width值，包括px、em、百分比。它的初始值是auto，此时浏览器会检查元素是否设置了width属性值。如果有，则使用width的值作为flex-basis的值；如果没有，则用元素内容自身的大小。如果felx-basis不为auto，那么浏览器会忽略width值。

#### flex-grow

flex-grow是增长因子，值非负，定义了元素在主轴方向上分配剩余长度的比例。

> 剩余长度等于弹性容器主轴长度减去所有flex项目flex-basis及外边距的总和。

如果一个弹性子元素的flex-grow值为0，那么它的宽度不会超过flex-basis的值；如果某个弹性子元素的增长因子非0，那么这些元素会增长占据所有的剩余长度。flex-grow的值越大，元素占据的剩余长度比例越大。

#### flex-shrink

flex-shrink是减少因子，值非负，定义了当felx项目溢出，元素在主轴方向上缩减自身长度的比例。

如果某个子元素为flex-shrink: 0，则不会收缩；如果值大于0，则会收缩至不再溢出。按照flex-shrink值的比例，值越大的元素收缩得越多。

### order

order属性能够改变项目的排列顺序，数值越小，顺序靠前，默认为0

### align-self

align-self属性控制flex项目在副轴上的对齐方式，可以覆盖容器的align-items属性。默认值为auto，表示继承容器的align-items属性。

align-self属性值与align-items属性值相同。