# React Hooks

react hooks能够加强函数组件的功能，使其替换class组件。

## 修改状态

### useState

useState能够让函数组件使用状态。该方法会返回一个数组，数组第一项为状态，第二项为改变状态的方法，下称setState。如果传入useState的值是个无参数函数，那么useState会执行该函数，将函数结果赋值给状态。

* 如果状态是个对象，setState方法不能局部更新属性，需要完全拷贝该对象。
* 如果传给setState的值必须与原来的值全等，状态不会更新
* 如果传给setState的值是个函数，setState会将旧状态传给该函数，将函数返回值作为新状态。在这种情况下，setState的聚合操作无法生效，连续的setState对状态的更新会生效。

### useRef

useRef有两个作用，获取dom元素、保存数据。useRef返回值是个对象，其current属性保存数据。

使用useRef保存数据在数据改变时不会触发组件渲染，而useState会改变视图。

### useReducer

useReducer接收两个参数，reducer和initial，返回一个数组，数组第一项为状态，第二项为发送action的方法，称为dispatch。reducer函数接收两个参数，state、action，通过action的信息与state进行计算返回新的state。

## 生命周期

### useEffect

useEffect能够给函数绑定生命周期函数。useEffect接收两个参数，无参函数和数组。无参函数如果返回一个函数，那么返回的函数会在组件销毁时被调用。

* 如果数组为空，则仅在组件挂载时执行无参函数
* 如果数组中元素是变量，那么当该变量变更时，无参函数会被执行
* 如果不传入数组，那么在每次更新组件时，无参函数会被执行。

### useLayoutEffect

useLayoutEffect与useEffect作用相同，唯一的区别是useLayoutEffect执行无参函数的时间是在组件渲染之前，会阻塞组件渲染。

## 性能优化

在React类组件中，性能优化是通过复写shouldComponentUpdate方法实现的。函数组件生命周期，因而不具备该方法。函数组件可以使用React.memo高阶组件实现性能优化，但React.memo是比较props属性值是否相等来确定渲染，如果props属性不是基本类型，那么依旧会渲染。

为了解决上述问题，根据props传入属性的类型不同，可以使用useMemo、useCallback。

### useMemo

useMemo能够保存对象的引用，当组件渲染时，useMemo返回的对象引用不变。useMemo接收两个参数，无参函数、数组，数组的元素作为依赖项，当依赖项发生改变，则无参函数会被调用。无参函数返回值是一个对象。

### useCallback

函数也是对象，因此可以使用useMemo保存引用，但是这种做法使得无参函数返回一个函数，不够简洁，useCallback作用与useMemo相同，但其第一个参数为函数，作为返回对象。

## 组件通信

### useContext

useContext可以获取到父组件上通过Provider暴露的数据，实现跨组件通信。