# React.js

React是Facebook在2013年推出的开源前端框架，具有函数式编程风格。

React16以上的版本叫React Fiber。

## 开发环境搭建

使用React有以下方式：

1. 在html中引入React.js文件

2. 通过脚手架工具编码，React官方提供了Create-react-app脚手架工具

   > 脚手架工具是辅助前端开发的工具，具有功能如：构建项目开发流程和目录、实现js文件的相互引用。使用脚手架工具开发的代码需要进行编译。

如果需要使用create-react-app脚手架搭建项目，需要在控制台输入下述命令

```
npx create-react-app [dirname]
```

脚手架工具安装完成后进入项目目录，在控制台输入`npm start`可以运行项目。

**项目目录**

* package.json

  文件中存储了项目的配置，文件中的scripts条目记录了能够运行的指令，eslintConfig记录项目的代码校验，browserslist记录项目支持浏览器的信息。

* public

  public中放置项目的html文件。

* src

  src目录放置项目的js、css文件，其中index.js是项目的入口文件。

## JSX语法

JSX是JavaScript的语法拓展，通过在js中的标签表示html元素，将该标签称为元素表达式。

在JSX语法中存在两类标签，html标签和组件标签。html标签名要小写，组件标签的首字母要大写。

JSX要求元素表达式必须存在一个根元素。React提供了Fragment标签，该标签具有占位符的作用，可以用于包裹元素表达式，且不会在页面上显示。

### 数据绑定

JSX可以将元素表达式中的属性值、文本与js数据进行绑定，要绑定的内容需要用花括号替换，在花括号内书写js变量或表达式。

* 绑定的js数据必须是this.state的属性。
* 一旦进行了数据绑定，绑定的内容只能和js数据相等，无法单独改变绑定的内容

### 事件绑定

JSX中可以对元素表达式触发的事件绑定处理函数，通过这种事件绑定可以改变js数据。

事件函数执行时，this指向undifined，需要使用bind修改指向。如果bind在花括号内，那么每次事件触发都会重新生成处理函数，降低性能，当处理函数不需要参数时，可以在构造函数内使用bind。

this.state中的数据不能直接改变，需要通过this.setState函数进行改变。

### 逻辑控制

**循环**

JSX中允许数据绑定中的数据是元素表达式，所以可以使用列表的map方法，对每个列表项返回一个元素表达式，实现循环渲染一个元素。这种循环遍历的方法可以作为函数调用。

对于循环的每一项需要使用`key`属性绑定列表项的唯一标识。

### 属性名

* JSX中的元素表达式要使用className作为类名，用于和class关键字区分
* label中需要使用htmlFor与input绑定

## 样式

在React中可以使用css书写元素的样式，在元素所在的js文件中需要使用import语法导入css文件。

## 生命周期函数

| 函数                  | 说明                               |
| --------------------- | ---------------------------------- |
| componentWillMount    | 挂载前执行                         |
| compoentDidMount      | 挂载后执行                         |
| shouldComponentUpdate | 更新前自动执行，返回值为true，更新 |
| componentWillUpdate   | 更新前自动执行                     |
| componentDidUpdate    | 更新后自动执行                     |
| componentWillUnmount  | 移除前执行                         |

