# 效果实现

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