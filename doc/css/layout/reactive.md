# 响应式布局

响应式布局是一个网站对不同屏幕尺寸有不同的布局的移动web开发方式，从而达成一个网站兼容多个终端的目的。

> 响应式布局能够使一个网站兼容多种终端，然而为了考虑兼容性，特定终端上会出现冗余代码，导致加载时间变长，效率低下。
>
> 响应式布局适用于结构简单的展示性质页面，不适用于结构复杂、交互多的功能性页面，如淘宝。

**原理**

通过媒体查询对不同屏幕尺寸设置断点实现响应式布局。

| xs     | sm     | md     | lg     | xl      |
| ------ | ------ | ------ | ------ | ------- |
| <576px | ≥576px | ≥768px | ≥992px | ≥1200px |

## 栅格系统

栅格系统是响应式布局的一种实现方式。栅格系统分为行和列，行和列构成的单元格是栅格，可以通过改变元素占据栅格的数量改变布局样式。

以下将实现一个12列的栅格系统。

### 使用示例

```html
<div class="container">
    <div class="row">
        <div class="col-12 col-sm-3 col-md-4 col-lg-6 col-xl-1">
            <img src="##">
        </div>
    </div>
</div>
```

其中col、col-sm、col-md、col-lg、col-xl用于响应特定屏幕尺寸，后面的数字表示占据的列数。如col-md-4表示在中屏容器宽度为父容器row的4/12。

**改变位置**

* 向右移动使用push特定词或者offset

  如col-md-push-4表示在中屏时容器向右移动4列

* 向左移动使用pull特定词

**改变显示**

* 显示使用block

  d-md-block表示在在中屏及以上时容器显示。

* 消失使用none

  d-xs-none可简写为d-none。

### 开发

栅格系统的开发需要借助媒体查询，栅格的形成有两种方法：float和flex。以下将主要以float开发栅格系统作为示例。

#### 基础部分

**container**

container类用于使元素居中显示，在不同屏幕尺寸下通过媒体查询改变width值实现不同的居中效果。

> flex开发中container无需改变

```css
.container{
    width:100%;
    margin-left:auto;
    margin-right:auto;
    padding-left:15px;/*避免row类元素溢出*/
    padding-right:15px;
}
@media(min-width:576px){
    .container{
        width:540px;
    }
}
```

**row**

row类元素用于包裹col类族元素，其中margin设置为负值用于在col元素中嵌套row类元素时无内边距。

```css
.row{
    margin-left:-15px;
    margin-right:-15px;
    /*flex开发时更改为
    display:flex;
    flex-wrap:wrap;
    margin-left:-15px;
    margin-right:-15px;
    */
}
```

**col类族**

col类族，如col-1、col-md-3等，用于设置在不同屏幕尺寸下的宽度。

```css
.col-1,.col-2,.col-12{
    float:left;
    /*flex开发时
    无需设置该样式
    */
}
/*flex开发时增加
.col{
    flex-basis:0;
    flex-grow:1;
    padding-left:15px;
    padding-right:15px;
    with:100%;
}
*/
.col-1{
    width:8.333333%;
    /*flex开发时更改为
    flex:0 0 8.3333333%;
    */
}
/*其他的col样式
*/
@media(min-width:576px){
    .col-sm-1,.col-sm-2,.col-sm-12{
        float:left;
    }
    .col-sm-1{
        width:8.333333%;
    }
}
/*
设置内边距
*/
.col-1,.col-12,.col-sm-1,.col-md-1,.col-lg-1,.col-xl-1{
    padding-left:15px;
    padding-right:15px;
    position:relative;
    /*flex开发时更改为
    padding-left:15px;
    padding-right:15px;
    with:100%;
    */
}
```

**清除浮动**

```css
.container:after,.row:after{
    content:"";
    display:table;
    clear:both;
}
/*flex开发时无需清除浮动
*/
```

#### 拓展部分

**改变位置**

```css
.col-offset-0{
    margin-left:0;
}
.col-offset-1{
    margin-left:8.333333%
}
.col-offset-12{
    margin-left:100%;
}
.col-push-0{
    left:auto;
}
.col-pull-0{
    right:auto;    
}
@media(min-width:576px){
    .col-sm-offset-0{
        margin-left:0;
    }
    .col-sm-push-0{
        left:auto;
    }
    .col-sm-pull-0{
        right:auto;
    }
}
/*flex中通过order改变位置，其中offset与float方法中一样
.col-order-first{
	order:-1;
}
.col-order-0{
	order:0;
}
*/
```

**改变显示**

```css
.d-none{
    display:none !important;
}
.d-block{
    display:block !important;
}
@media(min-width:576px){
    .d-sm-none{
    	display:none !important;
    }
    .d-sm-block{
        display:block !important;
    }
}
```

