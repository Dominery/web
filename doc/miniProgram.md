# 小程序

小程序是通过前端语言书写的网页，运行在一个应用上，能够通过主应用调用操作系统的API。

微信团队对小程序的运行环境做了改进，提高了网页代码执行速度，使得小程序的用户体验能够和原生应用相媲美。

## 代码组成

微信小程序中代码由四类文件组成：JSON、WXML、WXSS、JS。

### JSON

JSON文件保存页面的配置信息。

### WXML

wxml是一种模板语法，书写格式与html类似，由标签属性构成。

注意点：

* wxml标签必须闭合
* 属性大小写敏感

#### 数据绑定

wxml可以将属性值、文本与js数据进行绑定，将要绑定的内容用双重花括号替换，在花括号内书写js变量或表达式。

注意：

* 如果绑定属性值，属性值外需要有引号
* 绑定的js变量大小写敏感
* 如果绑定的变量值为undifined，则不会渲染
* 花括号内可以书写数字、字符串、数组

#### 逻辑控制

**条件逻辑**

wxml中可以使用`wx:if`属性决定是否渲染组件，如果该属性值为false则不渲染。

多个条件逻辑的实现，需要使用`wx:elif`、`wx:else`。

```wxml
<view wx:if = "{{isShow>1}}">
	当isShow大于1时存在
</view>
<view wx:elif = "{{isShow>0}}">
	当isShow大于0小于等于1存在
</view>
<view wx:else>
	当isShow小于等于0存在
</view>
```

**循环逻辑**

wxml可以使用`wx:for`属性绑定一个数组实现对组件的重复渲染，组件可以根据数组元素进行数据绑定，呈现不同效果。数组当前项下标变量默认为`index`，元素变量默认为`item`，这两个变量名分别可以通过`wx:for-item`、`wx:for-index`进行设置。

如果组件与数组元素进行数据绑定，那么需要在循环时设置`wx:key`，该属性值为数组元素的标识符。该标识符可以字符串或者*this。

```wxml
<block wx:for = "{{users}}" wx:key="*this">
	<view>hello</view>
	<view>{{item.name}} {{index}}</view>
</block>
```

#### 文件拆分

**模板**

wxml提供了模板template，用于封装能够重复使用的代码。模板内容在template标签中书写，需要设置`name`属性。导入模板需要指定文件路径，模板使用也要使用template标签，通过is传入模板名称。is可以进行数据绑定，能够实现动态渲染模板的功能。

```wxml
<import src="./content.wxml"/>
<template is="content-tpl" data="{{content:"hello world"}}"/>
```

content.wxml：

```wxml
<template name = "content-tpl">
	<view>{{content}}</view>
</template>
```

**include**

include能够将目标文件中除了template、wxs外的代码引入。

```wxml
<include src="./content.wxml"/>
```

使用include引入文件，content.wxml中可以使用{{}}语法访问data的数据。

### WXSS

WXSS是小程序的样式语言，基本类似于CSS。

#### 尺寸单位

WXSS提供了`rpx`尺寸单位，用于适配不同的设备。

在375px物理像素宽度的设备上，1rpx等于1px。

#### 引用

WXSS提供了`@import 'path'`语法导入外部wxss文件，这种导入方法会将外部文件编译到目标文件中。

#### 选择器

WXSS支持的选择器有限：类选择器、id选择器、元素选择器、伪元素选择器。这些选择器的优先级与CSS相同。

## 逻辑层

逻辑层的开发是书写一个对象，称该对象为逻辑对象。逻辑对象中存在某些函数，在特定时刻会被系统调用，这些函数称为生命周期函数。

微信在逻辑层提供了一个wx全局变量，提供api。

全局逻辑

在根目录的app.js书写的逻辑对象称为app，app中的属性和方法小程序内可视，页面可以通过getApp函数获得app，从而调用其方法或获取数据。

| 生命周期函数      | 特定时刻           |
| ----------------- | ------------------ |
| onLaunch(options) | 在小程序启动时     |
| onShow(options)   | 在小程序重新展示时 |
| onHide()          | 在小程序取消展示时 |
| onError(msg)      | 在脚本出错时       |

> onLaunch只有在内存开始加载小程序会执行，如果短暂切换到另一个应用后再切换回来，不会执行。
>
> onShow在进入小程序时会启动，onHide与之相反。

页面逻辑

在页面目录的js文件中书写的逻辑对象称为page，page作用域仅在该页面。

| 生命周期函数        | 特定时刻         |
| ------------------- | ---------------- |
| onLoad()            | 在页面启动时     |
| onRead()            | 页面启动并渲染后 |
| onShow()            | 页面展示时       |
| onHide()            | 页面取消展示     |
| onPullDownRefresh() | 下拉刷新时       |
| onReachBottom()     | 页面到最底部     |
| onShareAppMessage() | 点击分享时       |
| onPageScroll()      | 页面滚动时       |

页面的逻辑层与视图层能够借助page关联，page中定义一个data属性，data是个对象，里面的属性可以被视图层访问到。page中的方法可以被视图层绑定。

## 视图层

视图层的代码存在于后缀名wxml的文件中，代码书写形式类似于html，由组件构成。每个组件上可以书写属性，实现不同效果。

如果代码里出现`{{}}`则花括号内的代码作为js代码解析，解析时遇见标识符会从data的属性中寻找。
