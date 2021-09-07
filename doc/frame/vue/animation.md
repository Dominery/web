# 动画和过渡

vue中实现动画与过渡的方式有三种：基础实现、transition、状态动画。

## 基础实现

基础实现是通过css或者js控制元素上class存在与否实现的。这种方式需要对标签进行样式绑定。

## transition

vue提供了transition标签，用于实现控制元素组件的入场出场动画效果。

### 单元素组件

单元素组件需要被transition标签包裹，入场和出场需要依赖`v-if`、`v-show`或者动态组件。

如果是动态组件进行切换，默认两个组件会同时呈现动画效果，可以通过mode属性控制先后，mode属性值可以是out-in或in-out。

如果需要在网页打开时，单元素组件有动画效果，可以在transition上添加appear属性。

如果动画效果既有动画又有过渡，并且这两者的时长不一样，那么可以通过type属性或者duration属性控制整体时长。

type属性值可以是`transition`或`animation`。如果type属性为`transition`，那么当过渡结束，动画同时停止。duration属性值是整体时长，单位为毫秒。

#### 固定样式名

如果transition标签没有定义name属性，那么name属性默认为v。

通过书写固定名称的css样式可以实现动画效果。`-enter-`用来控制入场，`-leave-`用来控制出场。

```css
.v-enter-from{
    opacity:0;
}
.v-enter-active{
    transition:opacity .5s ease;
}
.v-enter-to{
    opacity:1;
}
```

#### 自定义样式名

transition提供了自定义样式名的方法，实现动画效果。开发者需要将样式绑定在transition的某些属性上。

```vue
<transtion
           enter-from-class=""
           enter-to-class=""
           enter-active-class=""
>
    <div v-show="show">
        hello world
    </div>
</transtion>
```

#### js实现

如果某些动画效果需要使用js，transition提供了支持js实现动画的机制。transition使用事件绑定的方式，当元素组件入场出场时会执行绑定的函数。所有绑定函数的第一个参数是元素组件，通过enter-active绑定的函数有第二个参数，该参数是一个函数，只有调用了这个函数，才会执行入场结束动画。

```vue
<transtion
           :css="false"
           @before-enter="hanleBeforeEnter"
           @enter-active="handleEnterActive"
           @after-enter="handleEnterEnd"
>
    <div v-show="show">
        hello world
    </div>
</transtion>
```

### 多元素组件

多元素组件需要被transition-group标签包裹。可以使用单元素组件的固定样式实现动画效果，同时多元素组件额外增加了v-move样式用来设置非变化元素组件的动画效果。

## 状态动画

状态动画是通过数据的变化实现的动画效果，