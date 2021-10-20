# CSS样式

## 基本样式

box-sizing

移动端的box-sizing通常为border-box，这是为了保证布局的稳定。移动端需要适配不同的视口大小，页面元素宽通常是百分比，如果box-sizing是content-box，则padding、border等会使元素宽高超过设定的值，破坏布局。

外边距margin不会因为border-box而被作为元素宽高一部分。

-webkit-tap-highlight-color

移动端的a标签元素点击后会有蓝色高亮背景，可以通过设置-webkit-tap-highlight-color为transparent去除这个背景。

## 媒体查询

@media在css2中可以针对不同媒体类型定制不同样式规则。CSS3 的多媒体查询继承了 CSS2 多媒体类型的所有思想： 取代了查找设备的类型，CSS3 根据设置自适应显示。

```css
@media not mediatype and (expression){
    
}
```

一条媒体查询语句由查询逻辑、媒体类型、媒体特征表达式组成。

### 查询逻辑

| 限定符 | 作用     |
| ------ | -------- |
| and    | 逻辑的与 |
| ，     | 逻辑的或 |
| not    | 逻辑的非 |

not的作用对象是由and连接的语句。

### 媒体类型

* all——所有设备
* print——打印机
* screen——显示设备
* speech——屏幕阅读器

### 媒体特征表达式

| 属性                       | 作用                                |
| -------------------------- | ----------------------------------- |
| with/max-width/min-width   | 查询媒体宽度                        |
| -webkit-device-pixel-ratio | 查询媒体设备像素比                  |
| orientation                | 查询横屏(landscape)或竖屏(portrait) |
| height                     | 查询媒体高度                        |
| aspect-ratio               | 视口的宽高比                        |

前三个属性使用频率更高。其中第2、4属性和第1个属性同样存在max、min等形式。



