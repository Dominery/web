# 移动端事件

移动端事件是用户与移动设备交互产生的。主要有三类事件，触摸事件、手势事件、传感器事件，其中后两者兼容性差，触摸事件使用最为广泛。

触摸事件存在touch事件、pointe事件，pointer事件的兼容性存在问题。

## touch事件

### 事件类型

| 事件        | 说明       |
| ----------- | ---------- |
| touchstart  | 触摸开始   |
| touchmove   | 移动       |
| touchend    | 触摸结束   |
| touchcancel | 触摸被中断 |

**注意**

1. touchstart必须要在元素内部才能够触发
2. 只有touchstart触发后，touchmove才会触发
3. touchstart触发后，不管touchmove、touchend是否在元素内，都会触发

### event对象

#### 触摸信息

手指触摸屏幕的信息可以通过三个event对象的属性获取到，touches、targetTouches、changedTouches，它们都是类数组对象，存储手指相关信息。

**区别**

touches中存储触摸在屏幕所有手指的信息，targetTouches中存储触摸绑定touch事件的元素的手指信息，changedTouches中存储触摸位置发生变化的手指的信息。

如果屏幕上只有一根手指触摸，那么在touchstart、touchmove中，三个类数组存储信息一致；touchend中，changedTouches会存储手指信息，而其余两个不会。所以一般情况下，使用changedTouches。

**changedTouches**

* clientX、clientY

  以可视区左上角为原点，触摸点到左边界的距离为clientX，到上边界的距离为clientY。

* pageX、pageY

  这两个数据是以页面左上角为原点测量得出，考虑到了滚动条的存在。

## 高级事件

touch事件是基础事件，对touch事件进行封装，可以实现双击、长按等高级事件。

hammer.js是高级事件的第三方库，可以从[官网](http://hammerjs.github.io/)下载。