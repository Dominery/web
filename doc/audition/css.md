# CSS面试题

## 布局

1. 盒模型宽度如何计算
2. margin纵向重叠问题
3. margin负值问题
4. BFC理解和应用
5. float布局问题，以及clearfix
   * 如何实现圣杯布局和双飞翼布局
6. flex实现三个点的色子

## 定位

1. absolute和relative分别依据什么定位？
2. 居中对齐有哪些实现方式

## 图文样式

1. 可继承属性

   字体属性（font-family及子属性）、文本属性（text-indent、text-align、line-height、color等）、visiblility、list-style、cursor

2. line-height继承问题

## 响应式

1. rem是什么？
2. 如何实现响应式

## CSS3

1. 关于动画

2. 新特性

   选择器、border-radius、gradient、过渡、动画

隐藏元素的方法有哪些？

| 方法                 | 特点                                   |
| -------------------- | -------------------------------------- |
| display:none         | 不在页面占据位置，不被渲染，不响应事件 |
| visibility:hidden    | 在页面占据位置，不被渲染，不响应事件   |
| opacity:0            | 在页面占据位置，被渲染，响应事件       |
| position:absolute    | 移出可视区                             |
| z-index:-1           | 置于页面底层，不响应事件               |
| transform:scale(0,0) | 不响应事件                             |

display:none和visibility:0的区别

* 是否占据页面空间
* 子元素是否能够显示
* 重排和重绘

