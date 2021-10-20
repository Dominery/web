# 效果实现

## 垂直居中

在容器里让内容垂直居中的最好方式是根据特定场景考虑不同因素。

* 自然高度的容器

  给容器加上相等的上下内边距让内容居中。

* 容器高度指定或避免使用内边距

  ```css
  .container{
      display: table-cell;
      vertical-align: middle;
  }
  ```

* 可以使用Flexbox

* 容器里面内容只有一行文字

  设置行高等于容器高度，如果内容不是行内元素，设置元素的display为inline-block。

* 容器高度明确

  * 内容高度明确

    绝对定位加margin-top负值

  * 自然高度的内容

    内容设置绝对定位，设置translate

## 文字溢出隐藏

**单行文字溢出隐藏**

```css
.text-ellipsis{
    overflow:hidden;
    text-overflow:ellipsis;
    white-space:nowrap;
}
```

flex布局不能和单行文字溢出一起使用。

**多行文字溢出隐藏**

多行文字溢出隐藏兼容性差，只能兼容webkit内核。

```css
.multiline-ellipsis{
    overflow:hidden;
    text-overflow:ellipsis;
    display:-webkit-box;
    -webkit-line-clamp:3;
    -webkit-box-orient:vertical;
    white-space:normal !important;
    word-wrap:break-word;
}
```

多行文字溢出不要与height一起使用。

