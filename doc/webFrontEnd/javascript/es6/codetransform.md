# 代码转换

## Babel

Babel是js编译器，用来将es6的代码转换成es6之前的代码。

Babel本身可以编译ES6的大部分语法，比如let、const、箭头函数、类，但是对于ES6新增的API，比如Set、Map、Promise等全局对象，以及一些定义在全局对象上的方法（比如Object.assign/Array.from)都不能直接编译，需要借助其它的模块。

Babel一般需要配合Webpack 来编译模块语法

### CLI环境使用

[Babel官网](https://babeljs.io/)提供了多种使用Babel的方式。CLI是命令行模式，需要安装Node.js。

> Node.js是个与浏览器对应的js平台。npm是node包管理工具。
>
> 后端的JavaScript=ECMAScript + IO + File + 服务器端操作。

1. 初始化项目

   在项目目录使用npm init命令初始化

2. 安装babel

   > ```
   > npm install --save-dev @babel/core @babel/cli
   > ```
   >
   > --save-dev表示只在编译阶段需要，使用这种方式默认安装最新版，如果需要安装指定版本可以书写如下命令
   >
   > ```
   > npm install --save-dev @babel/core@7.11.0 @babel/cli@7.10.5
   > ```

3. 编译命令

   在package.json文件的script中添加下面的语句

   > ```
   > "build": "babel src -d lib"
   > ```
   >
   > 表示通过babel编译src目录下的js文件输出到lib目录目录中

   在命令行输入`npm run build`就会执行编译操作

4. 配置文件

   配置文件告诉babel需要转换的js版本。需要安装present-env包，里面存放了转换操作的代码。

   ```
   npm install @babel/preset-env --save-dev
   ```

   在根目录下创建.babelconfig.json文件，书写如下内容

   ```json
   {  "presets": ["@babel/preset-env"] }
   ```

## Webpack

webpack是静态模块打包器，当webpack处理应用程序时，会将所有这些模块打包成一个或多个文件。

> 模块：像js、css、图片、字体等都是webpack能够处理的模块
>
> 静态：存于本地的模块文件

### 使用

详细使用见[官方文档](https://www.webpackjs.com/guides/getting-started/)。

1. 初始化项目

2. 安装webpack需要的包

   ```
   npm install --save-dev webpack-cli@3.3.12 webpack@4.44.1
   ```

3. 配置webpack

   创建webpack.config.js文件用来存放配置信息

   ```javascript
   const path = require('path');
   
   module.exports = {
     entry: './src/index.js',
     mode:'development',//指定是开发模式,默认是生成模式
     output: {
         path: path.resolve(__dirname, 'dist'),
       filename: 'bundle.js'
     }
   };
   ```

4. 打包并测试

   在package.json文件的scripts中添加

   ```json
   "webpack":"webpack --config webpack.config.js"
   ```

### 核心概念

entry

output

loader

plugins

### 应用

处理css文件

处理图片

搭建开发环境