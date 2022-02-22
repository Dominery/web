# 基本使用

## JSX语法

JSX是JavaScript的语法拓展，通过在js中的标签表示html元素，将该标签称为元素表达式。

在JSX语法中存在两类标签，html标签和组件标签。html标签名要小写，组件标签的首字母要大写。

JSX要求元素表达式必须存在一个根元素。React提供了Fragment标签，该标签具有占位符的作用，可以用于包裹元素表达式，且不会在页面上显示。

如果要在JSX中使用变量或表达式，需要用花括号包裹。如果需要对元素的style属性进行赋值，花括号里要求是一个对象。JSX中插入的值默认会进行转义，如果需要插入原生html，dangerouslySetInnerHTML属性赋值为一个对象，该对象的__html属性为待插入值。

JSX使用小驼峰命名定义属性名称，为了和JS关键字区分，使用className替换class。

## 条件判断

JSX是一个表达式，可以作为函数参数或返回值。因此实现条件判断有如下方式：

* 使用返回JSX的函数
* 使用三元表达式
* &&

## 列表渲染

列表渲染使用map将数据构造成JSX返回，对于每一项数据要使用key进行标识。key能够提高React使用diff算法的更新效率。

## 事件

如果将类普通方法直接绑定到事件上，方法执行时，this指向undifined，解决该问题：使用bind修改指向或者使用静态方法。

```javascript
class Demo extends React.Component{
    constructor(props){
        super(props)
        this.clickHandler1 = this.clickHandler1.bind(this)
    }
    render(){
        return <div onClick={this.clickHandler}>Click</div>
    }
    clickHandler=()=>{
        console.log(this)
    }
    clickHandler1(){
        console.log(this)
    }
}
```

React事件对象是React封装的SyntheticEvent类型，拥有DOM事件所有功能，可以通过nativeEvent获取原生事件对象。React中所有事件都被挂载到document上。

事件处理函数参数列表最后一个用于接收event。

## props

props作用：

* 传递数据

* 传递函数

* 类型检查

  > 安装prop-types包，组件的propTypes属性赋值为一个对象，通过如下方式限定
  >
  > ```javascript
  > List.propTypes = {list:PropTypes.arrayOf(PropTypes.object).isRequired}
  > ```

## setState

* 不可变值

  state属性值要保证不可变，使用setState时保证使用一个临时值，否则会影响性能

* 可能是异步更新
  * 在方法中调用setState，更新时是异步，可以向setState传入回调函数作为第二个参数，获取最新值
  * 在setTimeout中调用setState，更新是同步的
  * 自定义的DOM事件，事件函数中setState是同步的

* 可能会合并
  
  * 异步更新时，传入对象，多次更新会被合并，传入函数，不会被合并

## 生命周期

![](../../images/react_life_cycle.png)