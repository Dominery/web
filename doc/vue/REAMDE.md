# Vue.js

vue是一个前端开发框架，利用了mvvm设计模式，使开发者能够面向数据编程，不用手动操作dom，给开发提供了便利。

> mvvm由model、view、viewModel的首字母缩写构成，存在数据、视图、视图数据连接三层。

## 开发环境搭建

Vue提供了两种使用方式：

1. 通过在html文件引入`https://unpkg.com/vue@next`
2. 通过脚手架工具编码，Vue官方提供了Vue-cli

Vue项目创建

1. `npm install -g @vue/cli`
2. 使用`vue create [dirname]`创建项目

引入vue之后，可以通过Vue.createApp函数创建vue应用，传入的参数表示该应用的根组件。

| 生命周期函数   | 执行时刻                 |
| -------------- | ------------------------ |
| beforeCreate() | 在实例生成之前           |
| create()       | 在实例生成之后           |
| beforeMount()  | 在页面渲染之前           |
| mounted()      | 在页面渲染之后           |
| beforeUpdate() | 在数据发生变化时         |
| updated()      | 数据变化，页面重新渲染后 |
| beforeUmount() | 在应用失效时             |
| umounted()     | 在应用失效，dom销毁后    |

## 模板语法

template中的内容需要遵循模板语法，该语法与html类似，但可以使用双重花括号插入js表达式。

### vue指令

vue指令是一种书写在模板中的指令，用于实现特定的功能。

| vue指令 | 意义                   |
| ------- | ---------------------- |
| v-html  | 通过html形式展示变量   |
| v-bind  | 使绑定的属性值为变量值 |
| v-if    | 通过变量控制显示与否   |
| v-once  | 变量只会被渲染一次     |
| v-on    | 事件绑定               |

指令简写

v-on简写@，v-bind简写：

事件修饰符

存在一些代码使用频率很高，vue把这些代码实现的功能用修饰符代替。

stop、prevent、self、capture、once、passive。

如果点击之后要阻止默认行为，可以使用@click.prevent。

按键修饰符

enter、tab、delete、up、down

鼠标修饰符

left、right、middle

精确修饰符

exact

输入修饰符

lazy、number、trim

动态属性

动态属性是属性值不固定，由一个变量控制。

模板语法示例

```javascript
{
    data(){
        return{
            message:"hello world",
            name:"title",
        }
    }
    methods:{
        handleClick(){}
    }
    template:`
		<div
			@click="headleClick"
			:[name] = "message"
		>
			{{message}}
		</div>
	`
}
```

vue中使用非箭头函数形式定义函数，则该函数内的this指向vue应用实例。

如果插值表达式中使用了方法，只要页面重新渲染，该方法就会被重新执行。

如果存在一个值依赖于其他变量，那么可以将这个值使用计算属性替代。计算属性是定义在computed内的方法，只有计算属性依赖变量发生变更，计算属性才会被重新执行。

如果存在某个变量发生改变时，需要执行一些异步操作，那么可以通过监听器实现。监听器是定义在watch内的方法。

当computed和method都能实现某个功能，建议使用computed，因为computed存在缓存。当computed和watcher都能实现某个功能，建议使用computed，因为computed更简洁。

样式绑定语法

在模板中通过class属性绑定样式，样式的变量类型可以是字符串、对象、数组，对象中的属性作为class名称，如果其属性值为true则绑定。

#### 条件

v-if和v-show都能实现控制元素显示的功能，但两者的实现机制不同。v-if是通过控制元素在dom上存在与否，v-show是控制元素display样式。

如果需要实现多分支的条件结构来控制元素显示，那么可以通过使用v-else-if、v-else等vue指令来进行控制，使用这些指令的语句必须贴合在一起。

#### 循环

v-for可以实现

如果循环使用的数据改变，那么页面会重新渲染，为了避免一些未改变的数据也被渲染，损耗性能，可以使用key指定是否需要重新渲染。

```vue
<div>
    <div v-for="(value,key,index) in listObj">
        {{value}} -- {{key}}
    </div>
    <div v-for = "(item,index) in listObj">
        {{index}} -- {{item}}
    </div>
</div>
```

页面数据变化

1. 使用数组的变更函数
2. 替换数组
3. 直接更新数组的内容

#### 事件绑定

## Mixin

mixin是一个对象，可以包含组件内的属性和方法。可以将mixin对象添加到组件中的mixins数组中，从而使组件能够使用mixin。

### 规则

如果组件和mixin中存在相同名称的数据和方法，那么组件中该数据或方法优先级更高。

如果组件和mixin都定义了生命周期函数，那么程序运行时，两者的生命周期函数都会被执行，mixin中的会先执行。

如果组件和mixin都存在相同的自定义属性，那么组件里的该属性优先级更高。自定义属性的优先级可以修改。

> 在模板中访问自定义属性需要通过this.$options.attr，attr是属性名

```javascript
app.config.optionMergeStrategies.number=(mixinValue,appValue)=>{
    return mixinValue || appValue;
}
```

