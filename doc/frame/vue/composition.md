# Composition API

vue3引入了Composition API，是为了解决vue中数据与操作数据的代码分离从而降低代码维护性的问题。

## Setup函数

setup函数执行时间在实例初始化之前，所以在该函数中不能使用this获取实例。

```javascript
setup(props,context){
    return{
        name:"vue",
        handleClick:()=>{
            
        }
    }
}
```

setup函数返回的对象属性和方法会成为实例的数据和数据函数，可以在模板中使用。在实例方法中可以通过`this.$options.setup()`调用setup函数。

### context

context中有三个属性，attrs、slots、emit。

attrs存储non-props属性的数据。slots存储插槽的数据。emit是一个用于产生事件的函数。

## 基础

### 响应式引用

如果页面的元素是通过数据渲染的，并且当数据变化时，该元素会重新渲染，那么称这个数据是响应式数据。通过setup导出的对象中的数据默认不是响应式，如果想要使这些数据成为响应式数据，那么需要使用vue提供的ref、reactive。

ref和reactive的原理通过proxy对数据进行封装，当数据变化时，触发模板内容变更。ref用于封装基础类型数据，reactive用于封装对象、数组等。

```javascript
setup(props,context){
    const {reactive,ref} = Vue;
    const name = ref("vue");
    const nameObj = reactive({name:"vue"});
    setTimeout(()=>{
        name.value = "vue3";
        nameObj.name="vue3";
    },2000);
    return{
        name,nameObj
    }
}
```

> ref函数返回的实例是proxy({value:"vue"})，reactive返回的实例是proxy({name:"vue"})

如果通过reactive创建对象，期望能够导出对象的属性，将该对象传给toRefs函数，对函数的返回值进行解构获取的数据具备响应式特性。

> 对于proxy({name:"vue"})的参数，toRefs函数会返回{name:proxy({value:"vue"})}

如果对toRefs函数返回值解构获取的数据可能为空，并且期望该数据具备响应式特性，那么可以用toRef代替toRefs。

```javascript
const name = toRef(nameObj,'name');
```

### computed

computed计算属性在vue3中可以从Vue中获取，从而实现在组件外部创建计算属性的功能。computed函数可以传入回调函数或者对象，返回一个ref对象。当对该对象访问value时，会调用回调函数或者对象的get方法，获取数据。

```javascript
const {computed,ref} = Vue;
const data = ref(3);
const mutipleyBy2 = computed(()=>data.value*2);
const mutiplayByTwo = computed({
    get:()=>data.value*2,
    set:param=>{
        data.value = param;
    }
});
console.log(mutiplyBy2.value);
mutiplyByTwo.value = 5;
```

### 侦听器

watch和watchEffect是Vue3提供的两种侦听器。

watch需要传入侦听的数据和回调函数，侦听数据类型可以是函数、数组、响应式数据，当数据改变时，watch会将当前值和旧值作为参数调用回调函数。watch是惰性的，当页面加载后不会执行。

watchEffect只需要传回调函数，它本身会感知回调函数内的代码依赖，当依赖发生改变时会调用回调函数。watchEffect是非惰性的，当页面加载后会立即执行。

watch和watchEffect都返回一个函数，当该函数被调用时，侦听器会销毁。

### 生命周期函数

Vue3提供了独立的生命周期函数用于实现与组件里的生命周期函数相同的功能，组件里生命周期函数名称前加“on”就是对应的独立生命周期函数。

| 组件里方法        | 独立函数            |
| ----------------- | ------------------- |
| `beforeMount`     | `onBeforeMount`     |
| `mounted`         | `onMounted`         |
| `beforeUpdate`    | `onBeforeUpdate`    |
| `updated`         | `onUpdated`         |
| `beforeUnmount`   | `onBeforeUnmount`   |
| `unmounted`       | `onUnmounted`       |
| `errorCaptured`   | `onErrorCaptured`   |
| `renderTracked`   | `onRenderTracked`   |
| `renderTriggered` | `onRenderTriggered` |
| `activated`       | `onActivated`       |
| `deactivated`     | `onDeactivated`     |