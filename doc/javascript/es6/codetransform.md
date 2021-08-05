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

2. 在顶层文件中使用import导入core-js/stable

### CLI环境使用

[Babel官网](https://babeljs.io/)提供了多种使用Babel的方式。CLI是命令行模式，需要安装Node.js。

> Node.js是个与浏览器对应的js平台。npm是node包管理工具。
>
> 后端的JavaScript=ECMAScript + IO + File + 服务器端操作。

1. 初始化项目

   在项目目录使用npm init命令初始化

2. 安装babel

   > ```
   > npm install --save-dev @babel/core @babel/cli @babel/preset-env
   > ```
   >
   > present-env包里面存放了转换操作的代码，cli包用于命令行使用，core包负责调度。
   >
   > --save-dev表示只在编译阶段需要，使用这种方式默认安装最新版，如果需要安装指定版本可以书写如下命令
   >
   > ```
   > npm install --save-dev @babel/core@7.11.0 @babel/cli@7.10.5
   > ```

3. 配置package.json

   在package.json文件的script中添加下面的语句

   > ```
   > "build": "babel src -d lib"
   > ```
   >
   > 表示通过babel编译src目录下的js文件输出到lib目录目录中

   在命令行输入`npm run build`就会执行编译操作

4. 配置文件

   配置文件告诉babel需要转换的js版本。

   在项目目录下创建babel.config.json文件，书写如下内容
   
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

3. 配置package.json

   在package.json文件的scripts中添加

   ```json
   "webpack":"webpack"
   //如果配置文件名不是webpack.config.js，需要指定文件
   "webpack":"webpack  --config webpack.config.js"
   ```

4. 配置webpack

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

5. 打包并测试

   使用`npm run webpack`执行打包操作。

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

#### 处理css文件

**内联到html文件**

1. 在工作目录初始化项目

2. 安装webpack包

3. 创建配置文件

4. 安装htmlWebpackPlugin

5. 安装css-loader、style-loader

6. 在配置文件中配置加载器

   ```javascript
   rules:[
       {test:/\.css$/,
       use:['style-loader','css-loader']}
   ]
   ```

   > 如果是多个加载器，处理顺序从右到左

**通过link引入**

步骤与前者类似，需要使用miniCssExtractPlugin，将style-loader替换为miniCssExtractPlugin.loader

#### 处理图片

只有本地图片需要考虑使用webpack。

**css中的图片**

css中的图片可以使用file-loader处理。

file-loader处理图片的两个阶段：1.复制图片 2.修改css中图片的路径。

1. 安装loader

2. 配置rules

   ```javascript
   {
       test:/\.(jpg|png|gif)$/,
       use:{
           loader:'file-loader',
           options:{
               name:'img/[name].[ext]'
           }
       }
   }
   ```

   如果图片路径不对，可以通过对loader配置options解决。

   ```javascript
   {
       test:/\.css$/,
       use:[{
           loader:MiniCssExtractPlugin.loader,
           options:{
               publicPath:'../'
           }
       },
       'css-loader']
   }
   ```

**html中的图片**

html中的图片需要使用html-withimg-loader处理，该loader会调用file-loader。

1. 安装loader

2. 配置rules

   ```javascript
   {
       test:/\.(jpg|png|gif)$/,
       use:{
           loader:'file-loader',
           options:{
               name:'img/[name].[ext]',
               esModuler:false
           }
       }
   },
   {
       test:/\.(htm|html)$/,
       loader:'html-withimg-loader'
   }
   ```

**js中的图片**

js中的图片同样可以借助file-loader处理。

js中引入图片的方式如下：

```javascript
import img from './img/logo.png'
```

**url-loader**

file-loader功能过于单一，url-loader提供了更多处理图片的功能，底层使用了file-loader。

```javascript
{
    test:/\.(jpg|png|gif)$/,
    use:{
        loader:'url-loader',
        options:{
            name:'img/[name].[ext]',
            esModuler:false,
            limit:10000 //将小于10k的图片转化为base64格式
        }
    }
}
```

#### 搭建开发环境

使用webpack-dev-server可以实现自动打包的效果，执行一遍后如果文件修改后保存会自动打包。打包后的文件保存在内存中，不在磁盘上生成文件。

1. 安装webpack-dev-server

2. 在package.json配置scripts

   ```json
   "scripts":{
       "webpack":"webpack",
       "dev":"webpack-dev-server --open chrome"
   }
   ```


## 模板引擎

art-template是一种模板引擎，用于在js中生成html代码。

### 使用

以下将简要说明art-template的使用。

1. 引入

   可以通过script标签在线引入

   ```html
   <script src="https://unpkg.com/art-template@4.13.2/lib/template-web.js"></script>
   ```

2. 规定模板

   在type为`"text/html"`的script标签中书写模板

   ```html
   <script type="text/html" id="tpl-student">
   	{{each students}}
   	<li>{{$value.name}} {{$value.age}}</li>
   	{{/each}}
   </script>
   ```

3. 获取模板

   art-template提供了一个template函数，返回根据模板生成的字符串，第一个参数是模板的id，第二个参数是传递的数据的对象。

   ```javascript
   const students = [
       {name:Bob,age:10},
       {name:Alice,age:18}
   ];
   template('tpl-students',{
       'students':students
   })
   ```

### 语法

[官方文档](http://aui.github.io/art-template/zh-cn/docs/)中记录了art-template的用法。模板中{{}}中内容称为输出，符合js代码规范。

#### 输出数据

* 数据获取

  1. $data可以获取到template函数的第二个参数，通过属性访问获取到传入的数据
  2. 直接书写数据的属性名获取数据。

* 转义

  如果数据是字符串，那么template函数会根据html规则进行转义。

  如果不希望数据被转义，那么在数据前加@。

#### 条件

```html
{{if age>=18}}
<p>成年</p>
{{else}}
<p>未成年</p>
{{/if}}
```

#### 循环

循环中可以通过\$value获取每个循环时的数据，$index获取循环的索引。

```html
<script type="text/html" id="tpl-student">
	{{each students}}
	<li>{{$value.name}} {{$value.age}}</li>
	{{/each}}
</script>
```

#### 子模板

art-template提供在模板中引入其他模板的功能，称引入的模板为子模板。

```html
<script type="text/html" id="tpl-1">
	{{include 'tp1-2'}}
</script>
<script type="text/html" id="tpl-2">
	<p>子模板</p>
</script>
```

如果一个应用场景是多个地方使用同一个有子模板的模板，但是要求子模板有一些差异，可以使用原始语法实现向子模板传参的功能。

```html
<script type="text/html" id="tpl-3">
	<% include('tpl-4',{page:首页}) %>
</script>
<script type="text/html" id="tpl-4">
	{{page}}
</script>
```

### webpack中使用art-template

1. 初始化项目目录，`npm init`

2. 安装webpack需要的包

   ```
   npm install --save-dev webpack-cli@3.3.12 webpack@4.44.1 art-template-loader@1.4.3 rt-template-webpack> html-webpack-plugin@4.3.0 
   ```

3. 安装art-template包

   ```
   npm install art-template@4.13.2
   ```

4. 修改package.json中scripts内容为`"start":"webpack"`

5. 配置webpack.config.js

6. 依据art-template语法书写各组件

7. 打包`npm start`