# CSS样式

box-sizing

移动端的box-sizing通常为border-box，这是为了保证布局的稳定。移动端需要适配不同的视口大小，页面元素宽通常是百分比，如果box-sizing是content-box，则padding、border等会使元素宽高超过设定的值，破坏布局。

外边距margin不会因为border-box而被作为元素宽高一部分。

## Flex布局

Flex，Flexible Box的缩写，表示有弹性的盒子。

### 基本概念

如果元素中display属性值设置为flex或inline-flex，那么称该元素采用了flex布局，该元素称为flex容器。flex容器中的子元素是容器成员，称为flex项目。

flex容器默认存在两根轴：主轴和交叉轴。

主轴水平方向，主轴起始点叫`main start`，主轴结束点叫`main end`；交叉轴垂直方向，交叉轴起始点叫`cross start`，交叉轴结束点叫`cross end`。

项目默认沿主轴排列，单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫`cross size`。

### flex容器的属性

* display

  display属性值可以是flex和inline-flex。这两者的区别是inline-flex的宽度取决于flex项目。

  > flex作用是将对象元素作为弹性盒子显示
  >
  > inline-flex作用是将对象作为内联块弹性盒子显示

* flex-direction

  | 属性值         | 作用                           |
  | -------------- | ------------------------------ |
  | row            | 主轴为水平方向，main start在左 |
  | row-reverse    | 主轴为水平方向，main start在右 |
  | column         | 主轴为垂直方法，main start在上 |
  | column-reverse | 主轴为垂直方向，main start在下 |

* flex-wrap

  默认情况，项目都排在主轴线上。flex-wrap属性规定如果一条轴线排不下，如何换行。

  | 属性值       | 作用             |
  | ------------ | ---------------- |
  | nowrap       | 不换行           |
  | wrap         | 换行，新行在下面 |
  | wrap-reverse | 换行，新行在上面 |

  > 不换行时，为了能在主轴线上显示所有的flex项目，flex项目会被压缩，即使flex项目设置了宽度。

* justify-content属性

  justify-content属性定义了项目在主轴上的对齐方式。

  | 属性值        | 作用                                                     |
  | ------------- | -------------------------------------------------------- |
  | flex-start    | 左对齐                                                   |
  | flex-end      | 右对齐                                                   |
  | center        | 居中                                                     |
  | space-between | 两端对齐，项目间隔相等                                   |
  | space-around  | 两个项目两侧的间隔相等，项目间间隔比项目与边框间隔大一倍 |

* align-items属性

  align-items属性定义项目在交叉轴上如何对齐，默认为stretch。

  | 属性值     | 作用                                       |
  | ---------- | ------------------------------------------ |
  | flex-start | cross start 对齐                           |
  | flex-end   | cross end对齐                              |
  | center     | 交叉轴垂直居中                             |
  | baseline   | 项目第一行文字基线对齐                     |
  | stretch    | 如果项目未设置高度或设置auto，占满容器高度 |

* align-content属性

  align-content属性定义了多跟轴线在交叉轴上的对齐方式。属性值与align-items的属性值一样。

* flex-flew

  flex-flew是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。

### 项目属性

* order

  order属性定义项目的排列顺序，数值越小，顺序靠前，默认为0

* flex-grow

  flex-grow属性定义项目放大比例，如果存在剩余空间，依据flex-grow属性值扩张，默认为0。

  如果有的项目有flex-grow属性，有的项目有width属性，有flex-grow属性的项目将分配剩余空间。

* flex-shrink

  flex-shrink属性定义了项目缩小比例，如果容器缩小，项目会按照flex-shrink值缩小，默认为1。

  如果项目flex-shrink属性值为0，那么该项目不会缩小。

* flex-basis

  flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间。

* align-self

  align-self属性允许单个项目有与其他项目不一样的对齐方式，可以覆盖容器的align-items属性。默认值为auto，表示继承容器的align-items属性。

  align-self属性值与align-items属性值相同。

* flex

  flex属性是flex-grow、flex-shrink、flex-basis的简写，默认值为0 1 auto。如果属性值为auto，则表示1 1 auto；属性值为none，表示0 0 auto。