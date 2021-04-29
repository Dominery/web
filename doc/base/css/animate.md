# 过渡与动画

### 过渡

transition过渡属性是cSS3浓墨重彩的特性，过渡可以为一个元素在不同样式之间变化自动添加“补间动画”。

* 兼容性

  过渡从IE10开始兼容，移动端兼容良好。

* 优点

  动画细腻，内存开销小。

#### 基本使用

* 使用

  transition属性有4个要素，分别表示需要过渡的属性、动画的总时长、变化速度曲线、延迟时间。

  如果需要所有属性都参与过渡，可以将第一个要素写成all。all会引发效率问题，一般将需要过渡的属性写出，每个属性需要有四个要素，以逗号隔开。
  
  如果没有设置时间，那么默认是没有过渡效果的。

  ```css
  transition : width 1s linear 0s ;
  ```
  
  > 所有数值类型的属性，都可以参与过渡，比如width 64height、left、top、border-radius
  >
  > 背景颜色和文字颜色都可以被过渡
  >
  > 所有变形（包括2D和3D)都能被过渡
  
* 小属性

  | 属性                       | 意义           |
  | -------------------------- | -------------- |
  | transition-property        | 需要过渡的元素 |
  | transition-duration        | 动画时间       |
  | transition-timing-function | 动画变化曲线   |
  | transition-delay           | 延迟时间       |


#### 过渡的缓动效果

* 常用的缓动参数

![](../../../images/timing-func.png)

* 自定义动画缓动参数

  缓动参数是一种cubic-bezier(贝塞尔曲线)

  贝塞尔曲线可以通过网站https://cubic-bezier.com/生成

### 动画

动画效果实战