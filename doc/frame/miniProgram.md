# 小程序

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

语法

条件

```wxml
<view wx:if = "{{isShow}}">
	当isShow为true时存在
</view>
```

循环

```wxml
<block wx:for = "{{users}}" wx:key="*this">
	<view>hello</view>
	<view>{{item.name}} {{index}}</view>
</block>
```

> block作用如同占位符，在渲染后不存在

文件拆分

wxml有两种方法引入其他wxml的代码。

* include

   ```wxml
   <include src="./content.wxml"/>
   ```
   
   content.wxml中可以使用{{}}语法访问data的数据，而这会导致两个问题。

   1. content.wxml中{{}}内的变量与data属性名一致，关联性高。
2. 在通过include语法引入时，不能即时确定content.wxml中变量的值

* template

  微信提供了template语法用于解决include的问题。

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