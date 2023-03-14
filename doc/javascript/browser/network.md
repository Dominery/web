# 网络通信

## XMLHttpRequest 对象

AJAX 是 Asynchronous JavaScript and XML 的缩写，指的是通过 JavaScript 的异步通信。

AJAX 包括以下几个步骤。

1. 创建 XMLHttpRequest 实例
2. 发出 HTTP 请求
3. 接收服务器传回的数据
4. 更新网页数据

`XMLHttpRequest`对象是 AJAX 的主要接口，用于浏览器与服务器之间的通信。

1. 对`XMLHttpRequest`使用`new`命令生成实例。
2. 使用`open()`方法指定建立 HTTP 连接的一些细节。
3. 对onreadystatechange属性设置回调函数
4. 使用`send()`方法，实际发出请求

AJAX 只能向同源网址（协议、域名、端口都相同）发出 HTTP 请求，如果发出跨域请求，就会报错。

### 实例属性

#### readyState

`XMLHttpRequest.readyState`返回一个整数，表示实例对象的当前状态。该属性只读。它可能返回以下值。

- 0，表示 XMLHttpRequest 实例已经生成，但是实例的`open()`方法还没有被调用。
- 1，表示`open()`方法已经调用，但是实例的`send()`方法还没有调用，仍然可以使用实例的`setRequestHeader()`方法，设定 HTTP 请求的头信息。
- 2，表示实例的`send()`方法已经调用，并且服务器返回的头信息和状态码已经收到。
- 3，表示正在接收服务器传来的数据体（body 部分）。这时，如果实例的`responseType`属性等于`text`或者空字符串，`responseText`属性就会包含已经收到的部分信息。
- 4，表示服务器返回的数据已经完全接收，或者本次接收已经失败。

#### onreadystatechange

`XMLHttpRequest.onreadystatechange`属性指向一个监听函数。`readystatechange`事件发生时（实例的`readyState`属性变化），就会执行这个属性。

如果使用实例的`abort()`方法，终止 XMLHttpRequest 请求，也会造成`readyState`属性变化，导致调用`XMLHttpRequest.onreadystatechange`属性。

#### response

`XMLHttpRequest.response`属性表示服务器返回的数据体（即 HTTP 回应的 body 部分）。它可能是任何数据类型，比如字符串、对象、二进制对象等等，具体的类型由`XMLHttpRequest.responseType`属性决定。该属性只读。

如果本次请求没有成功或者数据不完整，该属性等于`null`。但是，如果`responseType`属性等于`text`或空字符串，在请求没有结束之前（`readyState`等于3的阶段），`response`属性包含服务器已经返回的部分数据。

#### withCredentials

`XMLHttpRequest.withCredentials`属性是一个布尔值，表示跨域请求时，用户信息（比如 Cookie 和认证的 HTTP 头信息）是否会包含在请求之中，默认为`false`

如果需要跨域 AJAX 请求发送 Cookie，需要`withCredentials`属性设为`true`。注意，同源的请求不需要设置这个属性。

为了让这个属性生效，服务器必须显式返回`Access-Control-Allow-Credentials`这个头信息。

#### responseType

`XMLHttpRequest.responseType`属性是一个字符串，表示服务器返回数据的类型。这个属性是可写的，可以在调用`open()`方法之后、调用`send()`方法之前，设置这个属性的值，告诉浏览器如何解读返回的数据。如果`responseType`设为空字符串，就等同于默认值`text`。

`XMLHttpRequest.responseType`属性可以等于以下值。

- ""（空字符串）：等同于`text`，表示服务器返回文本数据。
- "arraybuffer"：ArrayBuffer 对象，表示服务器返回二进制数组。
- "blob"：Blob 对象，表示服务器返回二进制对象。
- "document"：Document 对象，表示服务器返回一个文档对象。
- "json"：JSON 对象。
- "text"：字符串。

#### responseText

`XMLHttpRequest.responseText`属性返回从服务器接收到的字符串，该属性为只读。只有 HTTP 请求完成接收以后，该属性才会包含完整的数据。

#### responseXML

`XMLHttpRequest.responseXML`属性返回从服务器接收到的 HTML 或 XML 文档对象，该属性为只读。如果本次请求没有成功，或者收到的数据不能被解析为 XML 或 HTML，该属性等于`null`。

要求在发送请求前，`XMLHttpRequest.responseType`属性要设为`document`。

#### responseURL

`XMLHttpRequest.responseURL`属性是字符串，表示发送数据的服务器的网址。

#### status, statusText

`XMLHttpRequest.status`属性返回一个整数，表示服务器回应的 HTTP 状态码。请求发出之前，该属性为`0`。该属性只读。

`XMLHttpRequest.statusText`属性返回一个字符串，表示服务器发送的状态提示。

#### timeout, ontimeout

`XMLHttpRequest.timeout`属性返回一个整数，表示多少毫秒后，如果请求仍然没有得到结果，就会自动终止。如果该属性等于0，就表示没有时间限制。

`XMLHttpRequestEventTarget.ontimeout`属性用于设置一个监听函数，如果发生 timeout 事件，就会执行这个监听函数。

### 实例方法

#### open()

`XMLHttpRequest.open()`方法用于指定 HTTP 请求的参数，或者说初始化 XMLHttpRequest 实例对象。它一共可以接受五个参数。

- `method`：表示 HTTP 动词方法，比如`GET`、`POST`、`PUT`、`DELETE`、`HEAD`等。
- `url`: 表示请求发送目标 URL。
- `async`: 布尔值，表示请求是否为异步，默认为`true`。许多浏览器已经禁止在主线程设置false，只允许 Worker 里面使用。
- `user`：表示用于认证的用户名，默认为空字符串。该参数可选。
- `password`：表示用于认证的密码，默认为空字符串。该参数可选。

#### send()

`XMLHttpRequest.send()`方法用于实际发出 HTTP 请求。它的参数是可选的，如果不带参数，就表示 HTTP 请求只有一个 URL，没有数据体；如果带有参数，就表示除了头信息，还带有包含具体数据的信息体。

多种格式的数据，都可以作为它的参数。二进制数据，可以传ArrayBufferView、Blob；表单数据，FormData。

所有 XMLHttpRequest 的监听事件，都必须在`send()`方法调用之前设定。

#### setRequestHeader()

`XMLHttpRequest.setRequestHeader()`方法用于设置浏览器发送的 HTTP 请求的头信息。该方法必须在`open()`之后、`send()`之前调用。如果该方法多次调用，设定同一个字段，则每一次调用的值会被合并成一个单一的值发送。

该方法接受两个参数。第一个参数是字符串，表示头信息的字段名，第二个参数是字段值。

#### overrideMimeType()

`XMLHttpRequest.overrideMimeType()`方法用来指定 MIME 类型，覆盖服务器返回的真正的 MIME 类型，从而让浏览器进行不一样的处理。

#### getResponseHeader()

`XMLHttpRequest.getResponseHeader()`方法返回 HTTP 头信息指定字段的值，如果还没有收到服务器回应或者指定字段不存在，返回`null`。该方法的参数不区分大小写。

#### getAllResponseHeaders()

`XMLHttpRequest.getAllResponseHeaders()`方法返回一个字符串，表示服务器发来的所有 HTTP 头信息。格式为字符串，每个头信息之间使用`CRLF`分隔（回车+换行），如果没有收到服务器回应，该属性为`null`。如果发生网络错误，该属性为空字符串。

#### abort()

`XMLHttpRequest.abort()`方法用来终止已经发出的 HTTP 请求。调用这个方法以后，`readyState`属性变为`4`，`status`属性变为`0`。

### 事件监听

XMLHttpRequest 对象可以对以下事件指定监听函数。

- XMLHttpRequest.onloadstart：loadstart 事件（HTTP 请求发出）的监听函数
- XMLHttpRequest.onprogress：progress事件（正在发送和加载数据）的监听函数
- XMLHttpRequest.onabort：abort 事件（请求中止，比如用户调用了`abort()`方法）的监听函数
- XMLHttpRequest.onerror：error 事件（请求失败）的监听函数
- XMLHttpRequest.onload：load 事件（请求成功完成）的监听函数
- XMLHttpRequest.ontimeout：timeout 事件（用户指定的时限超过了，请求还未完成）的监听函数
- XMLHttpRequest.onloadend：loadend 事件（请求完成，不管成功或失败）的监听函数
- XMLHttpRequest.onreadystatechange: readyStateChange事件(请求状态改变)

#### upload

发送文件以后，通过`XMLHttpRequest.upload`属性可以得到一个对象，通过观察这个对象，可以得知上传的进展。主要方法就是监听这个对象的各种事件：loadstart、loadend、load、abort、error、progress、timeout。

### Navigator.sendBeacon()

用户卸载网页的时候，有时需要向服务器发一些数据。在`unload`事件或`beforeunload`事件的监听函数里面，使用`XMLHttpRequest`对象发送数据。很可能在请求即将发送的时候，页面已经卸载了，从而导致发送取消或者发送失败。

为了解决这个问题，浏览器引入了`Navigator.sendBeacon()`方法。这个方法还是异步发出请求，但是请求与当前页面线程脱钩，作为浏览器进程的任务，因此可以保证会把数据发出去，不拖延卸载流程。

`Navigator.sendBeacon()`方法接受两个参数，第一个参数是目标服务器的 URL，第二个参数是所要发送的数据（可选），可以是任意类型（字符串、表单对象、二进制对象等等）。

这个方法的返回值是一个布尔值，成功发送数据为`true`，否则为`false`。

该方法发送数据的 HTTP 方法是 POST，可以跨域，类似于表单提交数据。它不能指定回调函数。

## Fetch

fetch是一个发送网络请求的方法。

### 基本使用

```js
let promise = fetch(url, [options])
```

* url 要访问的URL
* options 可选参数method，header

fetch返回的是一个promise，promise状态为fulfiled时，值为response对象。

如果请求携带cookie，options需要添加`creadentials: "include"`字段。

#### POST

发送POST请求需要修改配置相应的options对象。

```js
let user = {
  name: 'John',
  surname: 'Smith'
};

let response = await fetch('/article/fetch/post/user', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json;charset=utf-8'
  },
  body: JSON.stringify(user)
});

let result = await response.json();
alert(result.message);
```

> body可以传入以下四种
>
> * 字符串
> * FormData
> * Blob
> * URLSearchParams

#### 下载进度

`fetch` 方法允许去跟踪 下载 进度。

跟踪下载进度需要使用 `response.body` 属性。它实现了 `ReadableStream` 接口，可以逐块（chunk）提供 body。

```js
// Step 1：启动 fetch，并获得一个 reader
let response = await fetch('https://api.github.com/repos/javascript-tutorial/en.javascript.info/commits?per_page=100');

const reader = response.body.getReader();

// Step 2：获得总长度（length）
const contentLength = +response.headers.get('Content-Length');

// Step 3：读取数据
let receivedLength = 0; // 当前接收到了这么多字节
let chunks = []; // 接收到的二进制块的数组（包括 body）
while(true) {
  const {done, value} = await reader.read();

  if (done) {
    break;
  }

  chunks.push(value);
  receivedLength += value.length;

  console.log(`Received ${receivedLength} of ${contentLength}`)
}

// Step 4：将块连接到单个 Uint8Array
let chunksAll = new Uint8Array(receivedLength); // (4.1)
let position = 0;
for(let chunk of chunks) {
  chunksAll.set(chunk, position); // (4.2)
  position += chunk.length;
}

// Step 5：解码成字符串
let result = new TextDecoder("utf-8").decode(chunksAll);
```

#### 中止

##### AbortController对象

AbortController对象是一个控制器对象。

- 具有单个方法 `abort()`，
- 单个属性 `signal`，我们可以在这个属性上设置事件监听器。

当 `abort()` 被调用时：

- `controller.signal` 就会触发 `abort` 事件。
- `controller.signal.aborted` 属性变为 `true`。

##### 结合fetch

fetch的options对象接收一个可选属性signal，将`controller.signal`传入，fetch会监听其`abort`事件，调用`controller.abort()`即可中止请求。

### Response对象

#### 属性

| 属性   | 说明                          |
| ------ | ----------------------------- |
| ok     | 布尔值，状态码为200-299则true |
| status | HTTP状态码                    |

##### headers

`response.headers` 是一个类似于 Map 的对象。

该对象可以使用`get()`获取值，同时实现了迭代器接口，可以遍历。

#### 方法

`Response` 提供了多种基于 promise 的方法，来以不同的格式访问 body。

| 方法          | 说明                   |
| ------------- | ---------------------- |
| text()        | 以文本形式返回         |
| json()        | 解析为JSON格式返回     |
| formData()    | 以FormData对象形式返回 |
| blob()        | 以Blob形式返回         |
| arrayBuffer() | 以ArrayBuffer形式返回  |

## 同源策略

如果两个网页的协议、端口、域名都相同，那么称这两个网页同源。

标准规定端口不同的网址不是同源，但是浏览器没有遵守这条规定。

同源策略是为了保证用户信息的安全，防止用户在网页A的数据被网页B获取。

如果非同源，共有三种行为受到限制。

> （1） 无法读取非同源网页的 Cookie、LocalStorage 和 IndexedDB。
>
> （2） 无法接触非同源网页的 DOM。
>
> （3） 向非同源地址发送 AJAX 请求，浏览器会拒绝接受响应。

### Cookie共享

Cookie是按域名区分的，只要网页的域名相同Cookie就可以共享。如果需要共享Cookie的网页一级域名相同，有两种方法实现Cookie共享。

1. 将这些网页的`document.domain`设置为一级域名
2. 设置Cookie时将domain属性设置为一级域名

如果需要共享Cookie的网页一级域名不同，那么需要借助CORS跨域。

### 多窗口通信

多窗口是指页面设置了iframe或者使用了window.open打开多个窗口。

如果父子窗口不同源，那么它们之间不能相互获取dom。

如果父子窗口一级域名相同，那么可以通过设置`document.domain`实现同源。

对于不同源的网站，可以使用片段识别符和跨文档通信API实现通信。

#### 片段识别符

片段标识符（fragment identifier）指的是，URL 的`#`号后面的部分。

父窗口可以把信息，写入子窗口的片段标识符。子窗口可以通过监听`hashchange`事件获取信息。

#### window.postMessage()

HTML5 为了解决这个问题，引入了一个全新的API：跨文档通信 API。

`postMessage`方法的第一个参数是具体的信息内容，第二个参数是接收消息的窗口的源（origin），即“协议 + 域名 + 端口”，如果源与目标窗口的源不同，不会发送信息。也可以设为`*`，表示不限制域名。

父窗口和子窗口都可以通过`message`事件，监听对方的消息。

`message`事件的参数是事件对象`event`，提供以下三个属性。

> - `event.source`：发送消息的窗口对象
> - `event.origin`: 消息发送方窗口的origin
> - `event.data`: 消息内容

通过postMessage可以实现LocalStorage跨域共享。

### AJAX跨域

同源政策规定，AJAX 请求只能发给同源的网址，否则就报错。

除了架设服务器代理（浏览器请求同源服务器，再由后者请求外部服务），有三种方法规避这个限制。

#### JSONP

JSONP 是服务器与客户端跨源通信的常用方法。最大特点就是简单易用，没有兼容性问题，老式浏览器全部支持，服务端改造非常小。

它的做法如下。

第一步，网页添加一个`<script>`元素，向服务器请求一个脚本，这不受同源政策限制，可以跨域请求。

第二步，服务器收到请求后，拼接一个字符串，将 JSON 数据放在函数名里面，作为字符串返回

第三步，客户端会将服务器返回的字符串，作为代码解析，因为浏览器认为，这是`<script>`标签请求的脚本内容。这时，客户端定义了的函数会执行，拿到服务器返回的 JSON 数据。

#### Websocket

WebSocket 是一种通信协议，使用`ws://`（非加密）和`wss://`（加密）作为协议前缀。该协议不实行同源政策，只要服务器支持，就可以通过它进行跨源通信。

```http
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
Origin: http://example.com
```

正是因为有了`Origin`这个字段，所以 WebSocket 才没有实行同源政策。因为服务器可以根据这个字段，判断是否许可本次通信。

#### CORS

CORS 是跨源资源分享（Cross-Origin Resource Sharing）的缩写。它是 W3C 标准，属于跨源 AJAX 请求的根本解决方法。相比 JSONP 只能发`GET`请求，CORS 允许任何类型的请求。

对CORS安全的首部字段集合

* Accept
* Accept-Language
* Content-Language
* Last-Event-ID
* `Content-Type`的值属于下列之一:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

##### 简单请求

简单请求服务器接收并响应，但返回的结果会被浏览器拦截。

> 简单请求：使用了GET、HEAD以及POST请求，并且只自定义对CORS安全的首部字段。

请求体需要携带Origin字段，服务器接收到一个资源的请求，服务器检查Origin源是否在许可范围内，如果在许可内，服务器就在响应头中设置字段`Access-Control-Allow-Origin`，那么这个资源就可以被跨域访问；如果不在许可内，服务器返回正常响应，浏览器会拦截抛出错误。称这个跨域方式为CORS跨域资源共享。

> 字段表明所允许跨域请求的域名，*是通配符,没有任何限制

IE10及以上版本的浏览器可以正常使用CORS。

##### 预检请求

浏览器拦截采用拦截返回结果的方式，但服务器对请求已经作出了处理，可能修改了数据库中的数据。预检请求可以避免跨域请求对服务器的用户数据产生未预期的影响。

非简单请求是需预检请求，预检请求需要使用OPTIONS方法发起预检请求，获知服务器是否允许该请求，允许后再发实际请求。

预检请求浏览器在请求体中会携带`Origin`、`Access-Control-Request-Method`、`Access-Control-Request-Headers`。

预检请求需要服务器单独对其进行响应，在响应头中需要添加如下字段。

```http
Access-Control-Allow-Origin: http://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
Access-Control-Max-Age: 86400
```

 `Access-Control-Allow-Methods` 表明服务器允许客户端使用 POST,GET 和 OPTIONS 方法发起请求。

`Access-Control-Allow-Headers` 表明服务器允许请求中携带字段`X-PINGOTHER` 与`Content-Type`。

`Access-Control-Max-Age` 表明该响应的有效时间为 86400 秒。

##### 携带Cookie的请求

如果一个请求中携带了Cookie，浏览器为了安全性会再次拦截。

解决Cookie跨域问题，需要请求头中携带`withCredentials: true`，服务器中要设置响应头字段`Access-Control-Allow-Credentials: true`。如果服务器设置了这个字段，那么`Access-Control-Allow-Origin`不能设置为`*`，必须指定明确的、与请求网页一致的域名。

同时，Cookie 依然遵循同源政策，只有用服务器域名设置的 Cookie 才会上传，其他域名的 Cookie 并不会上传，且（跨域）原网页代码中的`document.cookie`也无法读取服务器域名下的 Cookie。

##### Response Header

对于跨源请求，默认情况下，JavaScript 只能访问“安全的” response header：

- `Cache-Control`
- `Content-Language`
- `Content-Type`
- `Expires`
- `Last-Modified`
- `Pragma`

访问任何其他 response header 都将导致 error。

要授予 JavaScript 对任何其他 response header 的访问权限，服务器必须发送 `Access-Control-Expose-Headers` header。它包含一个以逗号分隔的应该被设置为可访问的非安全 header 名称列表。

## 前后端鉴权方式

### 基本概念

#### 认证

认证是验证当前用户的身份。

#### 授权

授权是用户授予第三方应用访问该用户某些资源的权限。

#### 凭证

凭证是实现认证和授权的媒介，标记用户的身份。

### Cookie + Session

Cookie是鉴权的一种凭证，Session是归属用户的资源。

session 是基于 cookie 实现的，session 存储在服务器端，sessionId 会被存储到客户端的cookie 中。

#### 认证流程

1. 用户第一次请求服务器的时候，服务器根据用户提交的相关信息，创建对应的 Session
2. 请求返回时将此 Session 的唯一标识信息 SessionID 存入Cookie返回给浏览器
3. 当用户第二次访问服务器的时候，请求会自动判断此域名下是否存在 Cookie 信息，如果存在自动将 Cookie 信息也发送给服务端，服务端会从 Cookie 中获取 SessionID，再根据 SessionID 查找对应的 Session 信息，如果没有找到说明用户没有登录或者登录失效，如果找到 Session 证明用户已经登录可执行后面操作。

#### 拓展性问题

 如果是服务器集群， 或者是跨域的服务导向架构， 这就要求session数据共享，每台服务器都能够读取session。

1. Nginx ip_hash 策略，服务端使用 Nginx 代理，每个请求按访问 IP 的 hash 分配，这样来自同一 IP 固定访问一个后台服务器，避免了在服务器 A 创建 Session，第二次分发到服务器 B 的现象。
2. Session复制：任何一个服务器上的 Session 发生改变（增删改），该节点会把这个 Session 的所有内容序列化，然后广播给所有其它节点。
3. 共享Session：将Session Id 集中存储到一个地方，所有的机器都来访问这个地方的数据。这种方案的优点是架构清晰，缺点是工程量比较大。另外，持久层万一挂了，就会单点失败；

### Token

#### Access Token

Access Token是访问资源接口（API）时所需要的资源凭证。由三个部分组成，uid(用户唯一的身份标识)、time(当前时间的时间戳)、sign（签名，token 的前几位以哈希算法压缩成的一定长度的十六进制字符串）。

##### 认证流程

1. 客户端使用用户名跟密码请求登录
2. 服务端收到请求，去验证用户名与密码
3. 验证成功后，服务端会签发一个 token 并把这个 token 发送给客户端
4. 客户端收到 token 以后，会把它存储起来，比如放在 cookie 里或者 localStorage 里
5. 客户端每次向服务端请求资源的时候需要带着服务端签发的 token
6. 服务端收到请求，然后去验证客户端请求里面带着的 token ，如果验证成功，就向客户端返回请求的数据

##### 特点

* 基于 token 的用户认证是一种服务端无状态的认证方式

#### Refresh Token

refresh token 是专用于实现无感刷新access token的 token。如果没有 refresh token，也可以刷新 access token，但每次刷新都要用户输入登录用户名与密码。

Access Token 的有效期比较短，当 Acesss Token 由于过期而失效时，使用 Refresh Token 就可以获取到新的 Token，如果 Refresh Token 也失效了，用户就只能重新登录了。

### JWT

JSON Web Token（缩写 JWT）是目前最流行的跨域认证解决方案。JWT是Token的具体标准。

#### 原理

用户认证通过以后，服务器生成一个 JSON 对象，发回给用户。以后，用户与服务端通信的时候，都要发回这个 JSON 对象。服务器完全只靠这个对象认定用户身份。

#### 组成

JWT由三个部分组成，分别是Header、Pyload、Signature，这三个部分会使用Base64URL转换成字符串后以`.`拼接。

##### Header

Header 部分是一个 JSON 对象，描述 JWT 的元数据，如签名算法、token类型。

##### Payload

Payload 部分也是一个 JSON 对象，用来存放实际需要传递的数据。

JWT官方规定了7个字段。

- iss (issuer)：签发人
- exp (expiration time)：过期时间
- sub (subject)：主题
- aud (audience)：受众
- nbf (Not Before)：生效时间
- iat (Issued At)：签发时间
- jti (JWT ID)：编号

JWT 默认是不加密的，任何人都可以读到，所以不要把秘密信息放在这个部分。

##### Signature

Signature 部分是对前两部分的签名，防止数据篡改。

首先，需要指定一个密钥（secret）。这个密钥只有服务器才知道，不能泄露给用户。然后，使用 Header 里面指定的签名算法（默认是 HMAC SHA256），按照下面的公式产生签名。

```javascript
 HMACSHA256(
   base64UrlEncode(header) + "." +
   base64UrlEncode(payload),
   secret)
```