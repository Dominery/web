# 组件

页面可以拆分成多个组件。在react中，如果一个类继承了React.Component，那么这个类称为组件。组件需要声明render方法，该方法返回值是通过JSX语法书写的html内容，要支持JSX语法需要引入React。

> 如果js中的某个内容是通过html的标签包裹，那么称这个内容为JSX。

将组件挂载到html上，需要引入ReactDom，通过其render函数实现，render函数接收两个值，JSX语法的单个标签和Dom元素，第一个值经处理后生成的Dom会被挂载到第二个值上。

**JSX语法**

在JSX语法中存在两类标签，html标签和组件标签。html标签名要小写，组件标签的首字母要大写。