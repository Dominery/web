# 文本与字体属性

### 常用文本样式属性

| 属性           | 作用                 | 属性值                            |
| -------------- | -------------------- | --------------------------------- |
| color          | 设置文本内容的前景色 | 英语单词、十六进制、rgb()、rgba() |
| font-size      | 设置文字字号         | 可以使用px、em                    |
| font-weight    | 设置文字粗细程度     | normal、bold                      |
| font-style     | 设置文字倾斜         | normal、italic                    |
| text-decration | 设置文本修饰线外观   | none、underline、line-through     |

* color

  英语单词、rgb()在开发中很少使用。

  十六进制表示法，如果每两个相同，可以简写，如黑色#000。

  rgba()表示法最后一位表示透明度，值0-1,0全透明

* font-weight

  normal等于400，bold值为700

### 字体属性

font-family用来设置网页字体，其属性值可以是列表项。

一般英语字体放到前面，后面字体是前面字体的后备字体。如果字体名称中有空格，必须用引号包裹。

```css
font-family:serif, "Times New Roman", "微软雅黑";
```

 字体通常必须是用户计算机汇总已经安装好的字体。如果用户计算机中没有，必须提供字体文件，通过@font-face方式定义字体。

> 字体文件根据操作系统和浏览器不同，有eot、 woff2、woff、ttf、svg文件格式，需要同时有这5种文件。
>
> 阿里巴巴提供了一套免费商用授权的普惠字体，网址https://www.iconfont.cn/webfont

### 段落和行相关属性

### 继承性