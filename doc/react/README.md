# React.js

React是Facebook在2013年推出的开源前端框架，具有函数式编程风格。

React16以上的版本叫React Fiber。

## 开发环境搭建

使用React有以下方式：

1. 在html中引入React.js文件

2. 通过脚手架工具编码，React官方提供了Create-react-app脚手架工具

   > 脚手架工具是辅助前端开发的工具，具有功能如：构建项目开发流程和目录、实现js文件的相互引用。使用脚手架工具开发的代码需要进行编译。

如果需要使用create-react-app脚手架搭建项目，需要在控制台输入下述命令

```
npx create-react-app [dirname]
```

脚手架工具安装完成后进入项目目录，在控制台输入`npm start`可以运行项目。

**项目目录**

* package.json

  文件中存储了项目的配置，文件中的scripts条目记录了能够运行的指令，eslintConfig记录项目的代码校验，browserslist记录项目支持浏览器的信息。

* public

  public中放置项目的html文件。

* src

  src目录放置项目的js、css文件，其中index.js是项目的入口文件。
