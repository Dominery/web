# 表单标签

所有HTML表单都以一个&lt;form>元素开始

```html
<form action = "save.php" method="post">
</form>
```

> action属性表示表单要提交到的后台程序的网址
>
> method属性表示表单提交的方式，有get或post

## input元素

### 单行文本框

使用type属性值被设置为text的&lt;input>元素可以创建单行文本框，它是一个单标签。

| 属性        | 说明                                 |
| ----------- | ------------------------------------ |
| value       | 表示已经填写好的值                   |
| placeholder | 表示提示文本，以浅色文字写在文本框中 |
| disabled    | 不能与元素交互                       |

### 密码框

使用type属性值被设置为password的&lt;input>元素可以创建密码框

### 单选框

使用type属性值被设置为radio的&lt;input>元素可以创建单选按钮

互斥的单选按钮应该设置它们的name为相同值

单选按钮要有value属性值，向服务器提交的就是value值

单选按钮如果加上了checked属性，表示默认被选中

### label标签

label标签用来将文字和单选按钮进行绑定，用户单击文字的时候也视为点击了单选按钮

* 在HTML4，label标签是通过for属性和单选按钮的id属性进行绑定的
* 在HTML5，可以将单选按钮放在label标签里面

### 复选框

使用type属性值被设置为checkbox的&lt;input>元素可以创建复选框

同组复选框应该设置它们的name为相同值

复选框要有value属性值，向服务器提交的就是value值

### 按钮

表单中常见三种按钮，它们也都是input标签，type属性值不同

| type   | 说明                            |
| ------ | ------------------------------- |
| button | 普通按钮，可以简写为&lt;button> |
| submit | 提交                            |
| reset  | 重置                            |

## 其余表单控件

### 下拉菜单

&lt;select>标签表示下拉菜单，&lt;option>是它内部的选项

### 多行文本框

&lt;textarea>表示多行文本框

rows和cols属性，用于定义多行文本框的行数和列数