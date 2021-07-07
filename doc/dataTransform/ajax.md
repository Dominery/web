# 数据传输

前后端数据传输的方式主要是ajax，传输数据需要符合一定格式，服务器才能解析，数据格式一般是json格式。

## Ajax

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
   
   如果是get请求，send方法的传入参数不会有对请求产生影响，为了兼容性，该参数为null。
   
   如果是post请求，send方法的传入参数会被请求体携带发送给服务器。这个传入参数的格式需要前后端统一，一般有以下两种方式：
   
   1. 数据格式与表单发送的数据格式一样，如`username=alex&age=18`
   2. 数据格式为json格式

> 如果xhr方法接收的参数中携带中文字符，为保证在不同浏览器上编码方式相同，从而后端能正确解码，需要将这些中文字符使用encodeURIComponent方法进行编码。

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

### XHR对象

#### 属性

| 属性            | 作用                                                         |
| --------------- | ------------------------------------------------------------ |
| responseType    | 设置返回的数据格式                                           |
| responseText    | 如果返回数据格式是Text，则属性值为该数据                     |
| response        | 对返回的数据按照设定的数据格式进行解析，属性值为解析后的数据，IE10兼容 |
| timeout         | 设置请求的超时时间，IE8兼容                                  |
| withCredentials | 指定使用ajax发送跨域请求携带cookie，IE10兼容                 |

> 使用ajax发送请求，不设置withCredentials时，同域会携带cookie，跨域不会。xhr设置了withCredentials，如果服务器响应头字段Access-Control-Allow-Origin值没有请求的域名，跨域会失败。

#### 方法

| 方法               | 说明           |
| ------------------ | -------------- |
| abort()            | 终止当前请求   |
| setRequestHeader() | 设置请求头信息 |

> 一般通过setRequestHeader方法设置Content-Type，从而告诉服务器传递的数据格式。
>
> ```javascript
> xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded');
> ```

#### 事件

* load

  load事件在响应数据可用时触发，可以替代readystatechange事件。

  load事件兼容到IE9，与addEventListner兼容性一致，因而两者可以搭配使用。

  ```javascript
  xhr.addEventListner(
  	'load',
      ()=>{
          if(xhr.status>=200&&xhr.status<300||xhr.status===304){
              //process
          }
      },
      false
  );
  ```

* error

  error事件在请求发生错误时触发。IE10兼容。

* abort

  abort事件在调用abort方法终止请求时触发。IE10兼容。

* timeout

  timeout事件在请求超时时触发。IE8兼容。

### 跨域

域名是ip地址的代号。

发送一个请求，如果请求的url的域名、端口号与协议与当前网站的这三者都相同，那么称这种请求方式为同域请求；如果有任何一者不同，那么称这种请求方式为跨域请求。跨域请求会被浏览器阻止。

阻止跨域请求是浏览器的一种安全策略。

#### 跨域解决方案

* CORS跨域资源共享

  服务器接收到一个资源的请求，如果服务器在响应头中设置字段Access-Control-Allow-Origin:*，那么这个资源就可以被跨域访问，称这个跨域方式为CORS跨域资源共享。

  > 字段表明允许所有的域名来跨域请求它，*是通配符,没有任何限制

  IE10及以上版本的浏览器可以正常使用CORS。

* JSONP

  JSONP利用script标签跨域不会被阻止的原理实现加载跨域资源。

  使用方式

  1. 服务器端准备JSONP接口
  2. 使用script标签加载接口
  3. 声明需要处理资源的函数

### 应用

#### 提交表单数据

使用ajax提交表单数据可以实现页面不跳转的功效。在获取表单数据时，可以使用模板字符串依次拼接，但是这种方法效率低效耦合度高，可以使用FormData简化操作。

* FormData

  FormData用于从表单dom元素中取出需要发送的数据，并将这些数据按照mutipart/form-data格式编码。IE10兼容。

  使用FormData时，xhr对象不需要设置请求头的Content-Type，在请求发送时，浏览器会添加上。

  ```javascript
  const data = new FormData(formDom);
  xhr.send(data);
  ```

  FormData有append方法可以用于额外添加数据。

## Json

json，全称是JavaScript Object Notation，是一种数据格式，主要用于规范ajax发送和接收的数据格式。

json格式的数据冗余信息更少，所以json替代了xml成为前后端数据传输格式。

### 三种形式

json有三种形式，这些形式各自与js中的数据类型相似，因此js和json之间转换相对容易。

#### 简单值形式

json简单值形式对应js中的基础数据类型。json中的简单值形式有数字、字符串、null、布尔值这四种。

1. json中字符串必须要使用双引号
2. json中没有注释

#### 对象形式

json对象形式对应js中的对象。对象中的属性和值格式需要符合json的简单值形式。

#### 数组形式

json数组形式对应js中的数组。数组中的元素格式必须符合简单值形式。

```json
[1,2,"ajax","json"]
```

### js中的json方法

#### 解码

通过JSON.parse()方法可以将json格式的数据解析成js中对应的数据格式。

#### 编码

通过JSON.stringify()方法可以将有对应json形式的js数据转换成json数据。

