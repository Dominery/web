# webpack

webpack是静态模块打包器，当webpack处理应用程序时，会将所有这些模块打包成一个或多个文件。

> 模块：像js、css、图片、字体等都是webpack能够处理的模块
>
> 静态：存于本地的模块文件

## 使用

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

### 搭建开发环境

webpack-dev-server集成了自动编译、自动刷新浏览器功能。使用webpack-dev-server可以实现自动打包的效果，执行一遍后如果文件修改后保存会自动打包。打包后的文件保存在内存中，不在磁盘上生成文件。

1. 安装webpack-dev-server

2. 在webpack配置中配置devServer属性

   ```javascript
   module.exports = {
       devServer = {
       contentBase: './public',
   }
   }
   ```

3. 在package.json配置scripts

   ```json
   "scripts":{
       "webpack":"webpack",
       "start":"webpack-dev-server --open chrome"
   }
   ```

#### 代理API

webpack dev server可以实现代理。

```javascript
module.exports = {
    devServer: {
        proxy: {
            // https://localhost:3000/api/user -> https://api.github.com/user
            '/api': {
                target: 'https://api.github.com',
                pathRewrite: {
                    '^/api': '',
                },
                // 不使用localhost:3000作为请求github的主机名
                changeOrigin: true,
            }
        }
    }
}
```

#### sourceMap

sourceMap为了解决编译后代码与开发代码不一致导致调试困难的问题。

sourceMap是一个映射，映射源代码和编译后的代码。

```javascript
module.exports = {
    devtool: 'source-map',
}
```

* eval-是否使用eval执行模块代码
* cheap - Source Map是否包含行信息

* module -是否能够得到 Loader处理之前的源代码

#### 热更新HMR

HMR(Hot Module Replacement)是为了解决页面刷新导致页面状态丢失的问题，实现页面不刷新的前提下，模块可以及时更新。

```javascript
import webpack = require('webpack');

module.exports = {
    devServer: {
      hotOnly: true,  
    },
    plugins: [
        new webpack.HotModuleReplacementPlugin()
    ]
}
```

上述配置后，对样式文件更新可以实现热替换，是因为在loader中已经实现了这部分逻辑，而js没有实现，依旧是刷新，这存在区别是因为css直接将内容替换就可以，而webpack不知道js的逻辑，无法帮我们替换。

```javascript
import createEdirtor from './editro';

module.hot.accept('./editor', ()=> {
    
})
```

热更新的处理代码在打包的文件中会被移除。

#### 多配置文件

项目开发时注重开发效率，生产环境注重运行效率，关注点的不同导致webpack配置存在差异。这个问题可以使用多配置文件进行解决。

merge是webpack-merge提供的函数，用于处理两个对象的合并，不同于Object.assign，merge会将属性名相同的数组、对象进行合并。

```javascript
const merge = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    
})
```

#### 输出文件名Hash

部署前端文件时一般会启用服务器的静态资源缓存，用户浏览器可以缓存这些资源，减少服务器压力。如果缓存时间设置过短，效果不明显；如果缓存时间设置过长，应用重新部署，无法及时更新到客户端。

为了解决更新不及时的问题，文件名使用Hash，当重新部署文件名发生变化，使缓存失效。

```javascript
module.exports = {
    output: {
        filename: '[name]-[contenthash:8].js'
    }
}
```

webpack提供了三种hash，hash、chunkhash、contenthash，表示不同程度的hash，分别是项目级、目录级、文件级。

1. hash：跟整个项目的构建相关，构建生成的文件hash值都是一样的，只要项目里有文件更改，整个项目构建的hash值都会更改。
2. chunkhash：根据不同的入口文件(Entry)进行依赖文件解析、构建对应的chunk，生成对应的hash值。
3. contenthash：由文件内容产生的hash值，内容不同产生的contenthash值也不一样。

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

webpack加载资源方式

1. 遵循ES Modules标准的import声明
2. 遵循Common JS标准的require函数
3. 遵循AMD标准的define函数和require函数

### 代码分割

webpack会将所有文件打包到一个bundle中，如果项目过大，这个bundle文件会很大，此时需要将代码分割到多个文件。

> 代码分割是网络传输时延和请求数量之间的平衡。如果每个模块都打包成一个文件，那么应用需要很多请求才能获取内容，且每个响应数据文件内容占比小，浪费带宽。如果所有模块打包成一个文件，网络传输时间很长，浪费浏览器闲置的请求。

#### 多入口打包

多页应用程序适用于多入口打包。

```javascript
module.exports = {
    entry: {
        index: '/src/index.js',
        home: '/src/home.js',
    },
    output: {
        filename: '[name].bundle.js'
    },
    plugins: [
        new HtmlWebpackPlugin({
            filename: 'index.html',
            chunks:['index'],
        }),
        new HtmlWebpackPlugin({
            filename: 'home.html',
            chunks:['home'],
        })
    ]
}
```

#### 提取公共模块

```javascript
module.exports = {
    optimazation: {
         // 分割代码块
    splitChunks: {
      chunks: 'all',
      /**
       * initial 入口 chunk，对于异步导入的文件不处理
          async 异步 chunk，只对异步导入的文件处理
          all 全部 chunk
        */
       // 缓存分组
      cacheGroups: {
          // 第三方模块
          vendor: {
              name: 'vendor', // chunk 名称
              priority: 1, // 权限更高，优先抽离，重要！！！
              test: /node_modules/,
              minSize: 0,  // 大小限制
              minChunks: 1  // 最少复用过几次
          },

          // 公共的模块
          common: {
              name: 'common', // chunk 名称
              priority: 0, // 优先级
              minSize: 0,  // 公共模块的大小限制
              minChunks: 2  // 公共模块最少复用过几次
          }
      }
    }
}
```

#### 按需加载

webpack使用动态导入方式实现按需加载，动态导入的模块会被自动分包。

在import()内添加`/* webpackChunkName: */`可以给打包的模块命名，如果名字相同，会被打到一起。

```javascript
import(/* webpackChunkName: 'history' */'./src/history').then({default} => {
    
})
```

## 核心逻辑

### 执行流程

一次完整的 webpack 打包大致是这样的过程：

初始化阶段

- 初始化参数，将命令行参数与 `webpack 配置文件` 合并、解析得到参数对象。
- 创建编译器对象，参数对象传给 webpack 执行得到 `Compiler` 对象。
- 开始编译，执行 `Compiler` 的 `run`方法开始编译。每次执行 `run` 编译都会生成一个 `Compilation` 对象。
- 确定入口，根据配置中的 entry 找出所有的入口文件，调用 `compilition.addEntry` 将入口文件转换为 `dependence` 对象

构建阶段

- 编译模块，根据 `entry` 对应的 `dependence` 创建 `module` 对象，调用 `loader` 将模块转译为标准 JS 内容，调用 JS 解释器将内容转换为 AST 对象，从中找出该模块依赖的模块，递归该步骤。

生成阶段

- 输出资源，所有模块分析完成后，执行 `compilation` 的 `seal` 方法根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 `Chunk`，再把每个 `Chunk` 转换成一个单独的文件加入到输出列表。
- 最后执行 `Compiler` 的 `emitAssets` 方法把生成的文件输出到 `output` 的目录中。

![](../../images/webpack-basic-flow.png)

### 具体流程

1. 搭建结构，读取配置参数

   > Webpack 本质上是一个函数，它接受一个配置信息作为参数，执行后返回一个 compiler 对象

2. 用配置参数初始化Compiler

   > Compiler存放`编译信息`和`生命周期 Hook`，而且是单例模式

3. 挂载配置文件中的插件

   > Webpack Plugin 其实就是一个类，在类中需要我们定制一个 apply 方法，在 `apply` 方法中订阅各种生命周期钩子，当到达对应的时间点时就会执行

4. 执行Compiler对象的run方法开始执行编译

   4.1 编译前触发run钩子

   4.2 执行compile函数启动编译

   4.3 compile函数创建compliation对象，执行其build方法

5. build方法根据配置文件中的`entry`配置项找到所有入口，将入口配置成对象，统一配置信息格式

6. 从入口文件出发，调用配置`loader`规则，对各模块进行编译

   > Loader 本质上就是一个函数，接收资源文件或者上一个 Loader 产生的结果作为入参，最终输出转换后的结果。

   6.1 把入口文件的绝对路径添加到依赖数组中，记录此次编译依赖的模块

   6.2 调用`buildModule`函数得到入口模块的的 `module` 对象 （里面放着该模块的路径、依赖模块、源代码等）

   ​	6.2.1 读取模块内容，获取源代码

   ​	6.2.2 创建模块对象

   ​	6.2.3 找到对应的`Loader`对源代码进行翻译和替换

   6.3 将生成的入口文件 `module` 对象 push 进 `this.modules` 中

7. `buildModule`方法中找到此模块依赖的模块，再对依赖模块进行编译

   7.1 先把源代码编译成AST

   7.2 在`AST`中查找`require`语句，找出依赖的模块名称和绝对路径

   7.3 将依赖模块的绝对路径push到依赖数组中

   7.4 生成依赖模块的模块id

   7.5 修改语法结构，把依赖的模块改为依赖模块id

   7.6 将依赖模块的信息push到该模块的`dependencies`属性中

   7.7 生成新代码，把转译后的源代码放到`module._source`属性

   7.8 对依赖模块进行编译

   7.9 对依赖模块编译完成后得到依赖模块的 `module 对象`，push 到 `this.modules` 中

   7.10 等依赖模块全部编译完成后，返回入口模块的 `module` 对象

8. 等所有模块编译完，`build`方法中根据模块间的依赖关系，组装代码块`chunk`

   > 一般来说，每个入口文件会对应一个代码块`chunk`，每个代码块`chunk`里面会放着本入口模块和它依赖的模块

9. 把各个代码块`chunk`转换成文件加入输出列表

10. 确定好输出内容后，根据配置的输出路径和文件名，将文件内容写入到文件系统，触发done这个钩子执行

## loader

loader可以让webpack能够处理非js文件的模块。loader是一个处理函数。

loader主要分为编译转换类(css-loader)、文件操作类型(file-loader)、代码检查类(eslint-loader)。

### loader配置

`Loader` 可以在 `webpack.config.js`里配置，这也是推荐的做法，定义在 `module.rules` 里：

```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      { test: /\.js$/, use: 'babel-loader' },
      {
        test: /\.css$/,
        use: [
          { loader: 'style-loader' },
          { loader: 'css-loader' },
          { loader: 'postcss-loader' },
        ]
      }
    ]
  }
};
```

每一条 `rule` 会包含两个属性：`test` 和 `use`，比如 `{ test: /\.js$/, use: 'babel-loader' }` 意思就是：当 `webpack` 遇到扩展名为 `js` 的文件时，先用 `babel-loader` 处理一下，然后再打包它。

`use` 的类型：`string|array|object|function`：

- `string`: 只有一个 `Loader` 时，直接声明 `Loader`，比如 `babel-loader`。
- `array`: 声明多个 `Loader` 时，使用数组形式声明，比如上文声明 `.css` 的 `Loader`。
- `object`: 只有一个 `Loader` 时，需要有额外的配置项时。
- `function`: `use` 也支持回调函数的形式。

### Loader类型

默认情况下，`webpack` 会把文件进行 `UTF-8` 编码，然后传给 `Loader`。通过设置 `raw`，`Loader` 就可以接受到原始的 `Buffer` 数据。

#### 同步loader

```js
module.exports = function(source) {
  const result = someSyncOperation(source); // 同步逻辑
  return result;
}
```

#### 异步loader

```js
module.exports = function(source) {
  // 告诉 webpack 这次转换是异步的
  const callback = this.async();
  // 异步逻辑
  someAsyncOperation(content, function(err, result) {
    if (err) return callback(err);
    // 通过 callback 来返回异步处理的结果
    callback(null, result, map, meta);
  });
};
```

### api

所谓 `Loader`，也只是一个符合 `commonjs` 规范的 `node` 模块，它会导出一个可执行函数。`loader runner` 会调用这个函数，将文件的内容或者上一个 `Loader` 处理的结果传递进去。同时，`webpack` 还为 `Loader` 提供了一个上下文 `this`。

#### this.callback()

在 `Loader` 中，通常使用 `return` 来返回一个字符串或者 `Buffer`。如果需要返回多个结果值时，就需要使用 `this.callback`，定义如下：

```js
this.callback(
  // 无法转换时返回 Error，其余情况都返回 null
  err: Error | null,
  // 转换结果
  content: string | Buffer,
  // source map，方便调试用的
  sourceMap?: SourceMap,
  // 可以是任何东西。比如 ast
  meta?: any
);
```

#### this.async()

获取loader的异步callback()，返回一个callback，使用方法同this.callback()。

#### this.cacheable()

有些情况下，有些操作需要耗费大量时间，每一次调用 `Loader` 转换时都会执行这些费时的操作。

在处理这类费时的操作时， `webapck` 会默认缓存所有 `Loader` 的处理结果，只有当被处理的文件发生变化时，才会重新调用 `Loader` 去执行转换操作。

`webpack` 是默认可缓存的，可以执行 `this.cacheable(false)` 手动关闭缓存。

#### this.resource

当前处理文件的完整请求路径，包括 `query`，比如 `/src/App.vue?type=templpate`。

#### this.resourcePath

当前处理文件的路径，不包括 `query`，比如 `/src/App.vue`。

#### this.emitFile

让 `webpack` 在输出目录新建一个文件

### 常用loader

#### babel-loader

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

#### css-loader

使用css-loader可以让webpack识别css文件，css-loader将css文件转换为一个js模块，将css代码以字符串表示，push到exports数组中。

style-loader和miniCssExtractPlugin.loader能够将css转换后的模块引入到html中。style-loader采用style标签形式表示样式，miniCssExtractPlugin.loader使用link将css内容分离。

```javascript
rules:[
    {test:/\.css$/,
    use:['style-loader','css-loader']}
]
```

> 如果是多个加载器，处理顺序从右到左

#### url-loader

url-loader能够将资源转换为data urls，减少网络请求。

> data urls是一种协议，该协议包含协议名、媒体类型、编码方式和文件内容，能够直接作为链接被浏览器识别。

url-loader依赖file-loader，对于超出大小限制的文件，url-loader会调用file-loader。

```c++
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

#### file-loader

file-loader可以将用到的文件复制到打包后的目录下，并修改路径，通常用来处理css中的图片。

#### html-loader

html-loader处理在html文件中的图片地址，该loader会调用file-loader。

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
    loader:'html-loader'
    options: {
        attrs: ['img:src', 'a:href']
    }
}
```



## plugins

loader被用于帮助webpack处理各种模块，而插件则可以解决其他自动化工作。webpack 在编译过代码程中，会触发一系列 Tapable 钩子事件，插件所做的，就是找到相应的钩子，往上面挂上自己的任务，也就是注册事件。

### 钩子

钩子的本质就是：事件。为了方便我们直接介入和控制编译过程，webpack 把编译过程中触发的各类关键事件封装成事件接口暴露了出来，这些接口被很形象地称做：`hooks`（钩子）。

#### Tapable

Tapable 为 webpack 提供了统一的插件接口（钩子）类型定义，它是 webpack 的核心功能库。webpack 中目前有十种 `hooks`。

```js
// https://github.com/webpack/tapable/blob/master/lib/index.js

exports.SyncHook = require("./SyncHook");
exports.SyncBailHook = require("./SyncBailHook");
exports.SyncWaterfallHook = require("./SyncWaterfallHook");
exports.SyncLoopHook = require("./SyncLoopHook");
exports.AsyncParallelHook = require("./AsyncParallelHook");
exports.AsyncParallelBailHook = require("./AsyncParallelBailHook");
exports.AsyncSeriesHook = require("./AsyncSeriesHook");
exports.AsyncSeriesBailHook = require("./AsyncSeriesBailHook");
exports.AsyncSeriesLoopHook = require("./AsyncSeriesLoopHook");
exports.AsyncSeriesWaterfallHook = require("./AsyncSeriesWaterfallHook");
```

Tapable 还统一暴露了三个方法给插件，用于注入不同类型的自定义构建行为：

- `tap`：可以注册同步钩子和异步钩子。
- `tapAsync`：回调方式注册异步钩子。
- `tapPromise`：Promise方式注册异步钩子。

webpack 里的几个非常重要的对象，`Compiler`, `Compilation` 和 `JavascriptParser` 都继承了 Tapable 类。

#### Compiler Hooks

Compiler 编译器模块是创建编译实例的主引擎。大多数面向用户的插件都首先在 Compiler 上注册。

| 钩子         | 类型              | 什么时候调用                                                 |
| :----------- | :---------------- | :----------------------------------------------------------- |
| run          | AsyncSeriesHook   | 在编译器开始读取记录前执行                                   |
| compile      | SyncHook          | 在一个新的compilation创建之前执行                            |
| compilation  | SyncHook          | 在一次compilation创建后执行插件                              |
| make         | AsyncParallelHook | 完成一次编译之前执行                                         |
| emit         | AsyncSeriesHook   | 在生成文件到output目录之前执行，回调参数： `compilation`     |
| afterEmit    | AsyncSeriesHook   | 在生成文件到output目录之后执行                               |
| assetEmitted | AsyncSeriesHook   | 生成文件的时候执行，提供访问产出文件信息的入口，回调参数：`file`，`info` |
| done         | AsyncSeriesHook   | 一次编译完成后执行，回调参数：`stats`                        |

#### Compilation Hooks

Compilation 是 Compiler 用来创建一次新的编译过程的模块。一个 Compilation 实例可以访问所有模块和它们的依赖。在一次编译阶段，模块被加载、封装、优化、分块、散列和还原。

| 钩子                 | 类型            | 什么时候调用                                                 |
| :------------------- | :-------------- | :----------------------------------------------------------- |
| buildModule          | SyncHook        | 在模块开始编译之前触发，可以用于修改模块                     |
| succeedModule        | SyncHook        | 当一个模块被成功编译，会执行这个钩子                         |
| finishModules        | AsyncSeriesHook | 当所有模块都编译成功后被调用                                 |
| seal                 | SyncHook        | 当一次compilation停止接收新模块时触发                        |
| optimizeDependencies | SyncBailHook    | 在依赖优化的开始执行                                         |
| optimize             | SyncHook        | 在优化阶段的开始执行                                         |
| optimizeModules      | SyncBailHook    | 在模块优化阶段开始时执行，插件可以在这个钩子里执行对模块的优化，回调参数：`modules` |
| optimizeChunks       | SyncBailHook    | 在代码块优化阶段开始时执行，插件可以在这个钩子里执行对代码块的优化，回调参数：`chunks` |
| optimizeChunkAssets  | AsyncSeriesHook | 优化任何代码块资源，这些资源存放在 `compilation.assets` 上。一个 `chunk` 有一个 `files` 属性，它指向由一个chunk创建的所有文件。任何额外的 `chunk` 资源都存放在 `compilation.additionalChunkAssets` 上。回调参数：`chunks` |
| optimizeAssets       | AsyncSeriesHook | 优化所有存放在 `compilation.assets` 的所有资源。回调参数：`assets` |

#### JavascriptParser Hooks

Parser 解析器实例在 Compiler 编译器中产生，用于解析 webpack 正在处理的每个模块。我们可以用它提供的 Tapable 钩子自定义解析过程。

| 钩子      | 类型         | 什么时候调用                                                 |
| :-------- | :----------- | :----------------------------------------------------------- |
| evaluate  | SyncBailHook | 在计算表达式的时候调用。                                     |
| statement | SyncBailHook | 为代码片段中每个已解析的语句调用的通用钩子                   |
| import    | SyncBailHook | 为代码片段中每个import语句调用，回调参数：`statement`,`source` |
| export    | SyncBailHook | 为代码片段中每个export语句调用，回调参数：`statement`        |
| call      | SyncBailHook | 解析一个call方法的时候调用，回调参数：`expression`           |
| program   | SyncBailHook | 解析一个表达式的时候调用，回调参数：`expression`             |

### 常用plugin

#### clean-webpack-plugin

该插件可以清除某个目录下的文件，常用于清除导出目录。

```c++
const {CleanWepackPlugin } = require('clean-webpack-plugin');

module.exports = {
    plugins: [
        new CleanWebpackPlugin(),
    ]
}
```

#### copy-webpack-plugin

该插件可以将一些静态资源复制到导出目录。

```javascript
const CopyWebpackPlugin = require('copy-webpack-plugin');

module.exports = {
    plugins: [
        new CopyWebpackPlugin([
            "public",
        ])
    ]
}
```

#### html-webpack-plugin

该插件用于在webpack处理完js文件后，将其引入到所需的html文件。

* 基本功能

  单页面只需要实例化一遍。

  多页面的基本条件：

  1. 实例化多个
  2. 给生成的html命名，默认index.html
  3. 引入的js文件名，默认全部引入，文件名为entry中的属性名，template文件可以使用模板语法接收传入的值`<% htmlWebpackPlugin.options.title %>`

  ```javascript
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  module.exports = {
      plugins:[
          new HtmlWebpackPlugin({
              template:'./index.html',
              filename:'index.html',
              chunks:['index'],
          }),
       new HtmlWebPackPlugin({
              template:'./search.html',
              filename:'search.html',
              chunks:['search']
          })
      ]
  }
  ```

* 其余功能

  可以在每个实例对象中添加minify属性，用于配置处理html文件的操作，以及其他html属性。

  ```javascript
  {
  	minify: {
  	//删除index.html 中的注释
      removeComments: true,
      //删除index.html中的空格
      collapseWhitespace: true,
  	//删除各种html标签属性值的双引号
      removeAttributeQuotes: true
  	},
  	title: 'demo app',
      meta: {
          viewport: 'width=device-width'
      }
  }
  ```

#### miniCssExtractPlugin

miniCssExtractPlugin可以将css模块抽离到一个文件，使用link方式引入。

```javascript
const MiniCssExtractPlugin = require('mini-css-extra-plugin');

module.exports = {
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    MiniCssExtractPlugin.loader,
                    'css-loader',
                ]
            }
        ]
    },
    plugins: [
        new MiniCssExtractPlugin,
    ],
}
```

#### CssMinimizerPlugin

使用miniCssExtractPlugin对css进行打包后的css文件没有压缩，在webpack5中需要使用CssMinimizerPlugin进行压缩。

```javascript
const CssMinimizerPlugin = require('css-minimizer-plugin')
module.exports = {
    optimization: {
        minimizer: [
            `...`, // 压缩js
            new CssMinimizerPlugin(),
        ]
    }
}
```

### 开发plugin

一个 webpack plugin 由如下部分组成：

1. 一个命名的 Javascript 方法或者 JavaScript 类。
2. 它的原型上需要定义一个叫做 `apply` 的方法。
3. 注册一个事件钩子。
4. 操作webpack内部实例特定数据。
5. 功能完成后，调用webpack提供的回调。

```javascript
// webpack要求插件必须是一个函数或者是有apply方法的对象
class MyPlugin{
    apply(compiler) {// compiler是webpack运行的核心对象，可以注册钩子
        // emit在即将输出时执行，compilation是打包上下文
        compiler.hooks.emit.tap('MyPlugin',complation => {
 		for (const name in compilation.assets) {
            // assets中存储所有的模块，可以通过source()方法获取到内容
            if (name.endsWith('.js')) {
                content = compilation.assets[name].source();
                const withoutComments = content.replace(/\/\*([^\n]|\n)*?\*\//g, '');
                compilation.assets[name] = {
                    source: ()=>withoutComments,
                    size: ()=>withoutComments.length,
                }
            }
        }           
        })
        
    }
}
```

## 性能优化

### 打包效率优化

1. 缩小编译范围，减少不必要的编译工作
2. 编译瓶颈
   * babel 等 loaders 解析阶段
   * js 压缩阶段

#### 缩小范围

* noParse，忽略未采用模块化的文件，因此jquery或lodash将不会被下面的loaders解析
* 通过modules指定目录寻找模块
* exclude，排除目录

```js
{
    resolve: {
        modules: [
            resolve('src'),
            resolve('node_modules'),
        ]
    },
    module: {
    noParse: /jquery|lodash/, // 忽略未采用模块化的文件，因此jquery或lodash将不会被下面的loaders解析
    rules: [
        {
            test: /\.js$/,
            include: [ // 表示只解析以下目录，减少loader处理范围
                resolve("src"),
                resolve(config.common.layoutPath)
            ],
            exclude: file => /test/.test(file), // 排除test目录文件
            loader: "happypack/loader?id=happy-babel" // 后面会介绍
        },
    ]
}
}
```

#### 打破瓶颈

##### 多进程压缩

webpack-parallel-uglify-plugin 能够把任务分解给多个子进程去并发执行，进而大幅提升js压缩速度。

```js
const ParallelUglifyPlugin = require('webpack-parallel-uglify-plugin');

// ...
optimization: {
    minimizer: [
        new ParallelUglifyPlugin({ // 多进程压缩
            cacheDir: '.cache/',
            uglifyJS: {
                output: {
                    comments: false,
                    beautify: false
                },
                compress: {
                    warnings: false,
                    drop_console: true,
                    collapse_vars: true,
                    reduce_vars: true
                }
            }
        }),
    ]
}
```

##### 并发编译

HappyPack 也能实现并发编译，从而可以大幅提升 loader 的解析速度。

```js
const HappyPack = require('happypack');
const happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length });
const createHappyPlugin = (id, loaders) => new HappyPack({
    id: id,
    loaders: loaders,
    threadPool: happyThreadPool,
    verbose: process.env.HAPPY_VERBOSE === '1' // make happy more verbose with HAPPY_VERBOSE=1
});

plugins: [
    createHappyPlugin('happy-babel', [{
        loader: 'babel-loader',
        options: {
            babelrc: true,
            cacheDirectory: true // 启用缓存
        }
    }])
]
```

##### 预先编译

webpack打包时，有一些框架代码是基本不变的，比如说 babel-polyfill、vue、vue-router、vuex、axios、element-ui、fastclick 等，这些模块也有不小的 size，每次编译都要加载一遍，比较费时费力。使用 DLLPlugin 和 DLLReferencePlugin 插件，便可以将这些模块提前打包。

##### 设置缓存

`babel-loader` 提供了 `cacheDirectory`特定选项（默认 `false`）：设置时，给定的目录将用于缓存加载器的结果。

### 产出代码优化

* 小图片base64

* bundle加hash

* 懒加载

* 提取公共代码

* IgnorePlugin

* 使用CDN加速

* 使用production

  如果mode设置为production，webpack会默认产生Tree-Shaking效果并开启代码压缩，Vue、React会自动删掉调试代码。

  如果使用babel-loader将module转换为commonjs，则Tree-Shaking不会生效。因为Tree-Shaking基于es6 Module的静态化，所有的依赖关系再编译时就能确定。

  > Tree-Shaking是通过判断es6 Module引入的模块中是否存在没有使用的代码块。

* Scope Hosting

  使用 scope hoisting 后会把需要导入的文件直接移入导入者顶部，这就是所谓的 hoisting。

  合并多个模块，代码体积减少、创建更少函数作用域、代码可读性增高
  
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
  

#### tree shaking

依赖收集：webpack在打包时，会将每个模块中的导出语句格式化后保存在一个数组中，并最终存储在`ModuleGraph`，它记录了模块间的依赖关系

标记：遍历所有模块，如果用到了收集来的导出的变量，将会打上已使用的标记

删除：通过默认的压缩工具 *taser* 的 *taser-webpack-plugin*，对于没有打标记的导出，就会被删除掉