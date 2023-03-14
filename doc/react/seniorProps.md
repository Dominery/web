## 高级特性

## 表单

HTML中，表单元素内部存在自己的状态，如果用React的state控制表单状态，以及使用事件绑定控制用户输入，那么称之为受控组件。

如果React没有控制表单状态，而是使用ref来获取表单内部状态，那么称之为非受控组件。

### 非受控组件

* ref
* defaultValue、defaultChecked设置初始值
* 必须操作DOM元素的情况下使用非受控组件

## Portals

Portals能够让组件渲染到父组件以外

```javascript
ReactDOM.createPortal(<Demo/>,document.body)
```

## context

Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据，例如当前认证的用户、主题或首选语言。

1. React.createContext()创建context对象，传入默认值
2. 传入数据使用context对象的Provider组件，value属性为共享数据
3. 消费数据：
   * class组件消费context对象定义静态属性contextType赋值为context对象，使用this.context获取共享数据，只能订阅单个Context
   * 函数组件使用useContext()传入context对象返回共享数据
   * 使用context对象的Consumer组件，组件内包裹一个回调函数接收value值，如果想要消费多个context，需要多层包裹

## 异步组件

* import()

* React.lazy
* React.Suspense

```javascript
const LazyDemo = React.lazy(()=>import('./lazy'))
function App(){
    return <React.Suspense fallback={<div>loading...</div>}>
             <LazyDemo />
          </React.Suspense>
}
```

## 性能优化

React中如果父组件更新，子组件无条件更新

* shouldComponentUpdate(简称SCU)

  SCU默认返回true，但可以进行订制，React把是否进行性能优化的权力交给开发者

  SCU必须配合不可变值，SCU通过比较setState参数和state是否相等决定是否渲染，可变值导致两者始终相等，无法渲染

* PureComponent、React.memo

  PureComponent用于class组件，memo用于函数组件，都在SCU实现浅比较

* 列表渲染使用key

* 自定义事件销毁

* 异步组件

* 减少函数bind this次数

## 高阶组件(HOC)

高阶组件是参数为组件，返回值为新组件的函数。高阶组件透传与自身无关的props。

## Render Props

具有 render prop 的组件接受一个返回 React 元素的函数，并在组件内部通过调用此函数来实现自己的渲染逻辑。