# React Hooks

Hooks 出现本质上原因是：

- 许多逻辑处理都要使用生命周期方法，但它们各自只有一个，并且跟 class 声明强行绑定，难以找到有效的实现逻辑复用的途径。
- 状态存储必须集中在一个 state 中进行管理，不易拆分。

## 基本使用

react v18新增了useSyncExternalStore、usetransition、useDeferredValue、useInetionEffect、useID。

### 数据驱动更新

#### useState

useState能够让函数组件使用状态，函数组件可以通过useState让组件重新渲染。

该方法会接收一个参数作为状态，返回一个数组，数组第一项为状态，第二项为改变状态的方法，下称setState。如果传入useState的值是个无参数函数，那么useState会执行该函数，将函数结果赋值给状态。

* 如果状态是个对象，setState方法不能局部更新属性，需要完全拷贝该对象。
* 如果传给setState的值必须与原来的值全等，状态不会更新
* 如果传给setState的值是个函数，setState会将旧状态传给该函数，将函数返回值作为新状态。在这种情况下，setState的聚合操作无法生效，连续的setState对状态的更新会生效。

#### useSyncExtermalStore

useSyncExternalStore 能够让 React 组件在 concurrent 模式下安全地有效地读取外接数据源，在组件渲染过程中能够检测到变化，并且在数据源发生变化的时候，能够调度更新。

useSyncExternalStore接收三个参数：subscribe、getSnapshot、getServerSnapshot。

* subscribe 为订阅函数，当数据改变的时候，会触发
* getSnapshot 可以理解成一个带有记忆功能的选择器。当 store 变化的时候，会通过 getSnapshot 生成新的状态值，如果该值改变的话那么会触发更新。这个状态值可提供给组件作为数据源使用。
* getServerSnapshot 用于 hydration 模式下的 getSnapshot。

#### usetransition

在 React v18 中，有一种新概念叫做过渡任务，这种任务是对比立即更新任务而产生的，一些影响用户交互直观响应的任务，需要立即更新；另一些不影响交互的任务更新可以滞后些。

useTransition 执行返回一个数组。数组有两个状态值：

- 第一个是处于过渡状态的标志——isPending。
- 第二个是 startTransition。可以把里面的更新任务变成过渡任务。

#### useDeferredValue

React 18 提供了 useDeferredValue 可以让状态滞后派生。useDeferredValue 的实现效果也类似于 transtion，当迫切的任务执行后，再得到新的状态，而这个新的状态就称之为 DeferredValue。

useDeferredValue 接受一个参数 value ，一般为可变的 state , 返回一个延时状态 deferrredValue。

#### useReducer

useReducer接收三个参数，reducer和initial，可选第三个参数是个函数，处理initial，返回一个数组，数组第一项为状态，第二项为发送action的方法，称为dispatch。

reducer函数接收两个参数，state、action，通过action的信息与state进行计算返回新的state，如果state内存指向相同，组件不会更新。

如果状态值修改逻辑很复杂，且需要复用，那么使用useReducer，而非useState。

### 状态获取与传递

#### useContext

可以使用 useContext 代替 context.Consumer 来获取 Provider 中保存的 value 值。

useContext 接受一个参数，context对象，返回值是context对象内部保存的value值。

#### useRef

useRef有两个作用，获取dom元素、保存数据。useRef返回值是个对象，其current属性保存数据。

使用useRef保存数据在数据改变时不会触发组件渲染，而useState会改变视图。

#### useImperativeHandle

useImperativeHandle 可以配合 forwardRef 自定义函数组件暴露给父组件的实例值。

useImperativeHandle 接受三个参数：

- 第一个参数ref: 接受 forWardRef 传递过来的 ref。
- 第二个参数 createHandle ：处理函数，返回值作为暴露给父组件的 ref 对象。
- 第三个参数 deps : 依赖项 deps ，依赖项更改形成新的 ref 对象。

### 状态派生与保存

在React类组件中，性能优化是通过复写shouldComponentUpdate方法实现的。函数组件无生命周期，不具备该方法。函数组件可以使用React.memo高阶组件实现性能优化。

#### useMemo

useMemo 可以在函数组件 render 上下文中同步执行一个函数逻辑，这个函数的返回值可以作为一个新的状态缓存起来。

useMemo接收两个参数，返回缓存值。

* create无参函数，函数返回值作为缓存值
* deps数组，存放当前 useMemo 的依赖项，在函数组件下一次执行的时候，会对比 deps 依赖项里面的状态，是否有改变，如果有改变重新执行 create ，得到新的缓存值。

#### useCallback

useMemo 和 useCallback 接收的参数都是一样，都是在其依赖项发生变化后才执行，都是返回缓存的值，区别在于 useMemo 返回的是函数运行的结果，useCallback 返回的是函数。

### 执行副作用

#### useEffect

useEffect能够给函数绑定生命周期函数。useEffect接收两个参数，无参函数和数组。无参函数如果返回一个函数，那么返回的函数会在组件销毁时被调用。无参函数会在组件渲染到屏幕之后延迟执行。

* 如果数组为空，则仅在组件挂载时执行无参函数
* 如果数组中元素是变量，那么当该变量变更时，无参函数会被执行
* 如果不传入数组，那么在每次更新组件时，无参函数会被执行。

#### useLayoutEffect

useLayoutEffect 和 useEffect 不同的地方是采用了同步执行。

* 首先 useLayoutEffect 是在 DOM 更新之后，浏览器绘制之前，这样可以方便修改 DOM，获取 DOM 信息，这样浏览器只会绘制一次，如果修改 DOM 布局放在 useEffect ，那 useEffect 执行是在浏览器绘制视图之后，接下来又改 DOM ，就可能会导致浏览器再次回流和重绘。而且由于两次绘制，视图上可能会造成闪现突兀的效果。
* useLayoutEffect callback 中代码执行会阻塞浏览器绘制。

#### useInsertionEffect

useInsertionEffect 的执行时机要比 useLayoutEffect 提前，执行的时候，DOM 还没有更新。本质上 useInsertionEffect 主要是解决 CSS-in-JS 在渲染中注入样式的性能问题。

### 工具

#### useID

useId 也是 React v18 产生的新的 hooks , 它可以在 client 和 server 生成唯一的 id , 解决了在服务器渲染中，服务端和客户端产生 id 不一致的问题，更重要的是保障了 React v18 中 **streaming renderer （流式渲染）** 中 id 的稳定性。

#### useDebugValue

useDebugValue 可用于在 React 开发者工具中显示自定义 hook 的标签。这个hooks目的就是检查自定义hooks。