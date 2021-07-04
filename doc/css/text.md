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

```css
@font-face {
    font-family: 'webfont';
    font-display: swap;
    src: url('fonts/webfont.eot');
    /* IE9*/
    src: url('fonts/webfont.eot') format('embedded-opentype'),
        /* IE6-IE8 */
        url('fonts/webfont.woff2') format('woff2'),
        url('fonts/webfont.woff') format('woff'),
        /* chrome、firefox */
        url('fonts/webfont.ttf') format('truetype'),
        /* chrome、firefox、opera、Safari, Android, iOS 4.2+*/
        url('fonts/webfont.svg') format('svg');
    /* iOS 4.1- */
}
```

> 字体文件根据操作系统和浏览器不同，有eot、 woff2、woff、ttf、svg文件格式，需要同时有这5种文件。
>
> 阿里巴巴提供了一套免费商用授权的普惠字体，网址https://www.iconfont.cn/webfont

### 字体图标的使用

1. 登录iconfont官网，选择图标，加入到购物车
2. 将选好的图标添加至项目
3. 在“我的项目”中点击下载至本地，解压后将字体文件复制到本地项目的fonts文件夹中
4. 夹根据demo_index.html的指引复制代码到css文件中，同时根据实际情况调整路径
5. 在使用的地方添加相应的编码和类样式名

### 段落和行相关属性

| 属性        | 作用                     | 属性值                         |
| ----------- | ------------------------ | ------------------------------ |
| text-indent | 首行文本内容之前的缩进量 | em为单位                       |
| line-height | 定义行高                 | px、没有单位的数值、百分数、em |
| text-align  | 设置文本对齐方式         | center、left、right            |

> 行高等于盒子高，可以让文字垂直居中

#### line-height

行高决定内联盒子高度;行间距，可大可小(甚至负值)，保证高度正好等同于行高。

**属性值说明**

* normal

  默认属性值，受用户浏览器和元素字体影响。

* 数值

  行高为当前元素的font-size大小的倍数。所有可继承的元素根据自身的font-size重新计算行高。

* 百分数

  相对于设置了该属性的元素font-size大小计算。当前元素根据font-size计算行高，将该数值继承给子元素。

**图片**

如果图片放在容器元素中，图片底部与父容器间存在间隙，如果改变行高，则会影响该间隙大小。

这种现象是因为浏览器默认图片底部需要与文字基线对齐，行高不会影响图片实际占据的高度。

消除图片底部间隙的方法

1. 图片块状化-无基线对齐

2. 底线对齐

   ```css
   img{vertical-align:bottom;}
   ```

3. 行高足够小-基线位置上移

**应用**

1. 图片水平垂直居中-IE8+

   ```css
   .box{line-height:300px;text-align:center;}
   .box>img{vertical-aligh:middle;}
   ```

2. 多行文本水平垂直居中-IE8+

   ```css
   .box{line-height:250px;text-align:center;}
   .box>.text{display:inline-block;line-height:normal;text-align:left;vertical-align:middle;}
   ```

   

#### font合写属性

font属性可以用来作为font-style、font-weight、font-size、line-height、font-family属性的合写。

```css
font:italic bold 20px/1.5 Arial,"Microsoft Yahei";
```

> font属性合写时，必须设置font-size和font-family才能生效。合写时有默认行高。

### 继承性

文本相关属性普遍具有继承性，只需要给祖先标签设置，即可在后代所有标签中生效。

> color 、font-、list-、text-、line-等属性有继承性

所以在开发中，通常设置body标签的字号、颜色、行高作为网页的默认样式。

* 就近原则

  在继承情况下，选择器权重计算失效，需要使用就近原则。如果选择器距离同样近，那么需要依靠选择器权重计算进行判断。

  > 如果选择器所选择的元素与目标元素的层级更少，则称这个选择器更近。