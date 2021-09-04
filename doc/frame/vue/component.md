# 组件

## 组件注册

## 组件通信

### 传值

如果一个组件需要根据调用方的需求改变数据，那么需要使用组件传值。组件传值是通过标签上的属性实现的。父组件与子组件约定一个属性作为数据传输的载体，该属性称为props属性，父组件将数据绑定在props属性上，子组件接收数据。props属性在最终的dom上不会显示。

#### Non-props属性

如果父组件绑定在子组件上的属性没有子组件中的props接收，那么这个是Non-props属性，该属性会在子组件的dom上显示。子组件可以通过inheritAttrs决定是否在dom上显示Non-props属性。

如果子组件有多个节点，则Non-props属性默认不显示，但子组件可以在节点上通过v-bind=“$attrs"使所有的Non-props属性显示。如果需要显示某个属性，可以使用如`:msg="$attrs.msg"`语法显示msg属性。

#### 数据绑定

数据绑定依据是否与变量绑定分为动态绑定和静态绑定。静态绑定传的数据和接收的数据都是字符串类型，而动态绑定可以传其他类型的数据。

如果需要向子组件传递很多参数，可以将这些参数作为一个对象的属性，通过动态绑定的方式传递该对象。

#### 数据接收

子组件接收数据需要借助props。props是数组或对象，用于接收自定义的props属性。

如果props是数组，则数组内的元素是props属性。如果props是对象，则对象内的属性名是props属性，属性值是数据类型或对象，用于校验数据。如果数据校验不通过，会在控制台上显示警告。

如果props属性名存在中划线，接收时需要变更为驼峰法对应的属性名。

```javascript
{
	props:{
		content: String,
		count:{
			type:Number,
			default:3,//required
			validator:function(value){
				return value<1000;
			}
		}
	},
}
```

#### 单项数据流

对于父组件向子组件传递的数据，子组件只能读取，无法修改。

子组件如果需要传递的数据作为初始值，显示的数据在初始值的基础上变化，那么可以在子组件的data中定义自己的数据。

### 事件

如果子组件需要对父组件的数据进行修改，子组件可以产生一个事件，父组件接收事件后再对数据进行修改。

#### 产生事件

子组件可以通过`this.$emit(name,para)`的方式产生一个`name`事件，并携带`para`参数。

为了管理子组件能够产生的事件，可以在子组件内定义一个emits数组，数组内存放事件名，如果子组件运行时产生的事件不在emits内，则会产生一个警告。emits可以是对象，属性名是事件名，属性值是校验函数。如果产生的事件没有通过校验，会产生警告。

#### 接收事件

父组件通过`@name="handler"`接收事件，name是事件名，需要使用中划线语法，handler是事件处理函数，如果事件携带参数，则该参数会传给handler。

#### v-model

如果子组件的数据依赖于父组件，同时也需要通过事件修改该数据，这种情况类似于双向绑定，vue提供了v-model及相关的语法简化代码实现。

```js
const app = Vue.createApp({
    data(){
        return{
            num:1
        }
    },
    template:`<counter v-model:count="num">`
});
app.component("counter",{
    props:["count"],
    methods{
    	handleClick(){
    		this.$emit("update:count",this.count);
		}
	},
    template:`<div @click="handleClick">{{count}}</div>`
})
```

> 如果不写`:count`，那么需要将子组件中的count替换为modelValue。

**修饰符**

v-model可以自定义修饰符，子组件通过在props对象中的modelModifier属性接收。modelModifier是一个对象，内部的default属性是一个函数，当没有修饰符时执行，返回值赋值给modelModifier。

### 内容传递

如果父组件需要传递某个模板内容给子组件，那么可以使用slot插槽。父组件调用子组件时使用双标签的形式，把需要传递的内容书写在标签内部，子组件可以通过`<slot></slot>`接收到该内容。

如果父组件没有传递内容，子组件的slot标签的内容则会显示。

如果父组件传递的内容需要在子组件内进行拆分，那么需要使用具名插槽，给每个子内容通过`v-slot`进行命名，子组件通过name属性指定子内容。

