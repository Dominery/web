# Ajax

ajax是Asynchronous JavaScript and XML (异步JavaScript和XML)的简写，用于浏览器和服务器间异步通信，实现在不重新加载整个页面的情况下，对页面某部分更新的操作。

> XML，可扩展标记语言，是一种数据格式，用于前后端数据传输。

### 基本用法

如果想要使用ajax进行浏览器与服务器间的通信，需要依赖XMLHttpRequest这个构造函数。

#### 使用步骤

1. 创建xhr对象

   实例化XMLHttpRequest，获取到实例对象，称其为xhr。

2. 监听事件

   服务器处理xhr发送的请求后，会向浏览器发送响应，浏览器获取到响应，响应的内容会填充到xhr响应的属性，同时触发xhr对象的readystatechange事件。

   > readystatechange事件监听readyState这个状态的变化，一共5个状态，用0-4标记
   > 0:未初始化。尚未调用open()
   > 1:启动。已经调用open(), 但尚未调用send()
   > 2:发送。已经调用send()， 但尚未接收到响应
   > 3:接收。已经接收到部分响应数据
   > 4:完成。已经接收到全部响应数据，而且已经可以在浏览器中使用了

3. 准备发送请求

   调用xhr的open方法，填充请求信息。

4. 发送请求

   调用xhr的send方法，向服务器发送请求，传入的参数作为请求体的数据。

#### 使用Ajax完成前后端通信

```javascript
const url = 'https://www.imooc.com/api/http/search/suggest?words=js';
const xhr = new XMLHttpRequest();
xhr.onreadystatechange = ()=>{
    if(xhr.readyState!==4)return;
    if((xhr.status>=200&&xhr.status<300)||xhr.status===304){
        console.log(xhr.responseText);
    }
};
xhr.open('GET',url,true);
xhr.send(null);
```

