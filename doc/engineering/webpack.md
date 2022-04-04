# webpack

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

loader可以让webpack能够处理非js文件的模块。loader是一个处理函数。

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

loader被用于帮助webpack处理各种模块，而插件则可以获取并构建文件信息。

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
       "start":"webpack-dev-server --open chrome"
   }
   ```

## 基本配置

### merge拆分

### 启动本地服务

### 处理es6

### 处理样式

### 处理图片

## 高级配置

### 多入口

### 抽离CSS文件

### 抽离公共代码

### 懒加载

### 处理JSX、Vue

## webpack性能优化

### 打包效率优化

可用于生产环境

* 优化babel-loader，开启缓存
* IgnorePlugin
* noParse
* happyPack
* ParallelUglifyPlugin

生产环境不可用

* 自动刷新
* 热更新
* DllPlugin

### 产出代码优化

* 小图片base64

* bundle加hash

* 懒加载

* 提取公共代码

* IgnorePlugin

* 使用CDN加速

* 使用production

  如果mode设置为production，webpack会默认产生Tree-Shaking效果并开启代码压缩，Vue、React会自动删掉调试代码。

  > Tree-Shaking是通过es6 Module引入的模块中如果存在没有使用的代码块，则该代码块不会被打包的现象。

* Scope Hosting

  代码体积减少、创建更少函数作用域、代码可读性增高

  ```javascript
  const ModuleConcatenationPlugin = require('webpack/lib/optimize/ModuleConcatenationPlugin')
  module.exports = {
      resolve: {
      mainFields: ['jsnext:main','browser','main']
    },
      plugins: [
      new ModuleConcatenationPlugin(),
    ],
  }
  ```

  