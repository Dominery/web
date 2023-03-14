# koa2

koa2是nodejs web server 框架，官网http://koa.bootcss.com。

## 环境搭建

koa2环境搭建需要借助脚手架koa-generator。

1. 通过npm安装脚手架`npm i -g koa-generator`
2. 通过`koa [dirname]`创建项目目录
3. 进入项目目录，运行`npm i`安装项目依赖

项目目录

* public

  public是默认存放静态文件的目录，`app.use(static(__dirname+'/public'))`指定了静态文件服务目录。

* views

  views是默认存放模板文件的目录。

路由创建

1. 在routes目录下新建路由文件
2. 在app.js中引入路由
3. 在app.js中注册路由

## 登录验证

登录验证功能实现需要借助cookie和session，session由后端持有，每个cookie对应一个session，session中存储客户信息。

要想在koa2中使用session，需要安装koa-generic-session包，并在app.js中进行配置。

```javascript
const session = require('koa-generic-session')
app.keys = ['ldg"./903HGH']//秘钥
app.use(session({
    cookie: {
        path: '/',
        httpOnly: true,//仅允许后端修改cookie
        maxAge: 24*60*60*1000//有效时间
    }
}))
```

进行上述配置后，可以在中间件中通过ctx.session访问session。

## 跨域请求

ajax跨域请求需要前后端都允许才能执行。在koa2中借助koa2-cors配置跨域请求。

```javascript
const cors = require('koa2-cors')
app.use(cors({
    origin: 'http://localhost:8080',//跨域请求的原网址
    credentials: true //允许跨域携带cookie
}))
```

## 连接数据库

对于MongoDB数据库，koa2可以使用mongoose进行连接。

```javascript
/**
 * @description mongoose 连接数据库
 */

const mongoose = require('mongoose')

const url = 'mongodb://localhost:27017'
const dbName = 'jingdong'
//开始连接
mongoose.connect(`${url}/${dbName}`, {
  useNewUrlParser: true,
  useUnifiedTopology: true
})
//连接对象
const db = mongoose.connection

db.on('error', err=>{
  console.error('mongoose',err)
})

module.exports = mongoose
```

## 中间件

### compose

```javascript
const compose = (...[first, ...other]) =>(...args) => {
    let result = first(...args);
    other.forEach(func => {
        result = func(result);
    });
    return result;
}

// 支持next的compose函数
function compose(middlewares) {
    return function(){
        dispatch(0);
    }
    function dispatch(i) {
        const func = middlewares[i];
        if (!func) return Promise.resolve();

        return Promise.resolve(
            func(function next(){
                return dispatch(i + 1);
            })
        )
   }
}
```

