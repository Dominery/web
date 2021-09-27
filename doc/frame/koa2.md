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