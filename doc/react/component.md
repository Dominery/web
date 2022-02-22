# 组件

页面可以拆分成多个组件。react中的组件可以通过两种方式声明，function和class。使用class方式需要类继承React.Component，function需要return一个jsx表达式。

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

父组件向子组件传值借助元素属性，子组件通过props接收父组件传递的值。

render函数执行的三种情况：

1. 组件创建
2. 组件的state属性内数据发生变更
3. 组件的props属性发生变更

如果需要获取元素的dom节点，那么在元素标签中增加ref属性，属性值是一个函数，函数参数是dom。如果ref属性在组件标签上，那么获取到的是组件实例。