# javaScript

JavaScript是由网景公司(Netscspe)在1995年开发的编程语言，前身是在客户端处理表单验证的LiveScript。

JavaScript特性更新速度很快，但用户使用的浏览器可能并不能支持这些特性。转换编译器，如Babel和Traceur，能够将前沿的js代码转换为等价的旧的js代码，从而解决这个问题。

## js特性

与C系、java等语言相比，JavaScript是一种更偏向函数式的语言。js中的一些概念与其他语言有着根本性差异。

### 一等公民的函数

在jst中，函数本质上是对象，可以通过字面量创建，可以赋值给变量，可以作为函数参数进行传递，可以作为返回值从其他函数返回。

### 函数闭包

### 基于原型的面向对象

## js体系

JavaScript语言由语法(ECMAScript)和API构成，根部不同场景下提供的api可以将JavaScript分为前端js、Nodejs、小程序中js等。

### ECMAScript

1997年,欧洲计算机制造商协会(ECMA)设置了JavaScript的标准，命名为ECMAScript。

ECMAScript就是对实现该标准规定的各个方面内容的语言的描述。规定了语言的组成部分：语法、类型、语句、关键字、保留字、操作符、对象。

JavaScript实现了ECMAScript语法的要求。

### 前端js

前端JavaScript体系由三部分组成。ECMAScript提供核心语言功能，DOM提供访问与操作页面的方法和接口，BOM提供与浏览器交互的方法和接口。

#### 文档对象模型(DOM)

文档对象模型（DOM, Document Object Model）是针对XML但经过扩展用于HTML的应用程序编程接口是，Web应用的结构化的UI表现形式

> 为了使Web开发领域技术一致，浏览器兼容，负责制定Web通信标准的W3C（WorldWideWebConsortium，万维网联盟）规划制定了DOM。

DOM把整个页面映射为一个多层节点结构。HTML或XML页面中的每个组成部分都是某种类型的节点，这些节点又包含着不同类型的数据。

#### 浏览器对象模型(BOM)

BOM没有建立统一的标准，每个浏览器都有自己的实现。

浏览器对象模型（BOM,Browser Object Model）是可以访问和操作浏览器窗口的应用程序编程接口。

开发人员使用BOM可以控制浏览器显示的页面以外的部分。