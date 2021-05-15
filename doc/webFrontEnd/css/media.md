# 多媒体查询

@media在css2中可以针对不同媒体类型定制不同样式规则。CSS3 的多媒体查询继承了 CSS2 多媒体类型的所有思想： 取代了查找设备的类型，CSS3 根据设置自适应显示。

### 查询语法

```css
@media not|only mediatype and (expression){
    
}
```

如果设备类型匹配多媒体类型并且表达式成立，则在设备上会显示指定样式效果。

限定词

* not——排除特定设备
* only——指定特别的设备

多媒体类型

* all——所有设备
* print——打印机
* screen——显示设备
* speech——屏幕阅读器