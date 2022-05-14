# 元素信息获取

## 样式

HTML中的样式有3种定义方式：外部样式、行内样式和内联样式。这三种样式都有相应的API去操作。

### 内联样式

#### style对象

元素节点的style属性是个对象，存放通过style设置的所有样式信息，js可以通过驼峰命名的css属性名从style对象中取出或者设置样式。

style对象还有下述属性。

| 属性                             | 说明                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| cssText                          | style属性中的css代码                                         |
| length                           | style属性中的css规则数                                       |
| getPropertyValue()               | 获取传入属性的值                                             |
| item()                           | 返回传入索引的css属性名                                      |
| removeProperty()                 | 删除传入的属性名                                             |
| setProperty(name,value,priority) | 设置CSS属性propertyName的值为value, priority是"important"或空字符串 |

cssText属性可以进行读取、赋值操作，赋值时会重写style属性的值。

#### 计算样式

document.defaultView上getComputedStyle()方法可以获取到CSSStyleDeclaration对象，对象存有元素继承的样式信息，该方法需要传入两个参数，dom元素和伪元素字符串(":after")，如果不需要查询伪元素，则第二个参数可以传null。

### 样式表

document.styleSheets表示文档中可用的样式表集合。

样式表由CSSStyleSheet类型表示，包括使用`<link>`元素和通过`<style>`元素定义的样式表。

CSSStyleSheet属性

| 属性                 | 说明                                  |
| -------------------- | ------------------------------------- |
| href                 | 行内样式，返回null，外部样式，返回url |
| disabled             | 样式表是否被禁用                      |
| cssRules             | 当前样式表包含的样式规则的集合        |
| deleteRule(idx)      | 在idx删除样式规则                     |
| insertRule(rule,idx) | 在idx插入规则字符串                   |

#### CSSRule

CSSRule类型表示样式表中的一条规则。CSSRule的cssText属性只读。

| 属性             | 说明                                                        |
| ---------------- | ----------------------------------------------------------- |
| cssText          | 返回整条规则的文本                                          |
| parentStyleSheet | 包含当前规则的样式表                                        |
| selectorText     | 返回规则的选择器                                            |
| style            | 返回CSSStyleDeclaration对象，可以设置和获取当前规则中的样式 |

## 元素尺寸

| 属性         | 说明                                       |
| ------------ | ------------------------------------------ |
| offetHeight  | 元素垂直方向占用的像素，包含边框、滚动条等 |
| offsetLeft   | 元素左边框到包含元素的距离，包含左边框     |
| offsetTop    | 元素上边框到包含元素的距离，包含上边框     |
| offsetWidth  | 元素水平方向占用的像素                     |
| offsetParent | 获取包含元素                               |
| clientWidth  | 元素宽度以content-box为准                  |
| clientHeight | 元素高度                                   |
| scrollHeight | 元素内容总高度                             |
| scrollWidth  | 元素内容总宽度                             |
| scrollTop    | 元素可视区顶部到元素顶部的距离             |
| scrollWidth  | 元素可视区左部到元素左部的距离             |

getBoundingClient()方法返回一个DOMRect对象，包含left、top、right、bottom、height、width等属性。