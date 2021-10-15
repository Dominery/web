# 组件

页面可以拆分成多个组件。在react中，如果一个类继承了React.Component，那么这个类称为组件。

组件需要声明render方法，该方法返回值是符合JSX语法的元素标签，要支持JSX语法需要引入React。

```js
import React,{Component} from 'react'
class App extends Compoent{
    render(){
        return (
            <div>
            	Hello world
            </div>

        )
    }
}
```

ReactDom的render函数实现将组件挂载到html上，render函数接收两个值，JSX语法的单个元素标签和dom节点，元素标签经处理后生成的dom会被挂载到dom节点上。

## JSX语法

JSX是JavaScript的语法拓展，通过在js中的标签表示html元素，将该标签称为元素表达式。

在JSX语法中存在两类标签，html标签和组件标签。html标签名要小写，组件标签的首字母要大写。

JSX要求元素表达式必须存在一个根元素。React提供了Fragment标签，该标签具有占位符的作用，可以用于包裹元素表达式，且不会在页面上显示。

如果需要在元素表达式中书写js表达式，那么JSX规定需要将js表达式用花括号包裹。