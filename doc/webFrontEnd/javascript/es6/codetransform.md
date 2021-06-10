# 代码转换

## Babel

Babel是js编译器，用来将es6的代码转换成es6之前的代码。

Babel本身可以编译ES6的大部分语法，比如let、const、箭头函数、类，但是对于ES6新增的API，比如Set、Map、Promise等全局对象，以及一些定义在全局对象上的方法（比如Object.assign/Array.from)都不能直接编译，需要借助其它的模块。

Babel一般需要配合Webpack 来编译模块语法

#### API替换

Babel只能在语法层面上进行替换，像Object.assign、Promise等API，Babel无能为力。如果想要替换这些api使之编译成es6之前的版本，需要引入额外的包。

1. 安装core-js

   ```
   npm install --save-dev core-js
   ```

2. 在顶层文件中使用impoert导入core-js/stable

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

   在根目录下创建babel.config.json文件，书写如下内容

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
     mode:'development',//指定是开发模式,默认是生产模式
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

#### entry

entry用来指定入口文件，其属性值可以是字符串或对象。

* 单入口文件

  可以使用字符串给出文件路径。

* 多入口文件

  使用对象保存文件路径数据

  ```
  entry:{
  	main:'./src/index.js',
  	search:'./src/search.js'
  }
  ```

#### output

output属性值是对象，对象里有path和filename两个属性，分别表示输出目录，输出文件名。

* 单出口文件

  filename属性值为出口文件名。

* 多出口文件

  filename属性值为`[name].js`，name会对entry中的属性名进行匹配。

#### loader

loader可以让webpack能够处理非js文件的模块。

**babel-loader**

babel-loader是webpack和babel的接口，可以让webpack先调用babel编译代码后在打包。

1. 安装babel相关包

   ```
   npm install --save-dev babel-loader@8.1.0 @babel/core@7.11.0 @babel/preset-env@7.11.0
   ```

2. 配置loader

   与output等同级添加module属性。

   ```javascript
   module.exports = {
       module:{
           rules:[{
               test:/\.js$/,//筛选文件
               exclude:/node_modules/,//排除文件
               loader:'babel-loader'
           }]
   	}
   }
   ```

#### plugins

loader被用于帮助webpack处理各种模块，而插件则可以用于执行范围更广的任务。

**html-webpack-plugin**

该插件用于在webpack处理完js文件后，将其引入到所需的html文件。

1. npm安装

   ```
   npm install --save-dev html-webpack-plugin
   ```

2. 配置插件

   导入插件后在module.exports中将其实例化添加到plugins属性值中。

   * 基本功能

       单页面只需要实例化一遍。

       多页面的基本条件：

       1. 实例化多个
       2. 给生成的html命名，默认index.html
       3. 引入的js文件名，默认全部引入，文件名为entry中的属性名

       ```javascript
       plugins:[
               new HtmlWebpackPlugin({
                   template:'./index.html',
                   filename:'index.html',
                   chunks:['index']
               }),
            new HtmlWebPackPlugin({
                   template:'./search.html',
                   filename:'search.html',
                   chunks:['search']
               })
           ]
       ```
   
   * 其余功能
   
     可以在每个实例对象中添加minify属性，用于配置处理html文件的操作。
   
     ```javascript
     minify: {
     	//删除index.html 中的注释
         removeComments: true,
         //删除index.html中的空格
         collapseWhitespace: true,
     	//删除各种html标签属性值的双引号
         removeAttributeQuotes: true
     }
     ```
   
     

### 应用

处理css文件

处理图片

搭建开发环境