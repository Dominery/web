# React基础

## 数据绑定

React能够将变量的值与dom元素的内容进行绑定，这称为响应式。

```js
class Data extends Component {
    constructor(props){
        super(props);
        this.state = {
            value:"hello world"
        }
    }
    render(){
        return (
            <div>{this.state.value}</div>
        )
	}
}
```

