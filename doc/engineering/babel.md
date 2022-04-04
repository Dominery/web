# babel

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

## 基础配置

.babelrc

```json
{
    "presets": ["@babel/preset-env"],
    "plugins": []
}
```

babel是通过plugins编译js语法，每个plugin对应一个语法。presets是plugins的集合。typescript、jsx语法都可以通过对应的presets编译为js语法。

## babel-polyfill

babel只处理语法，不处理jsAPI，这导致经过babel处理后的代码可能依旧不能在旧浏览器上运行。babel-polyfill通过es5支持新API，是core-js、regenerator的集合，已被弃用。

core-js是一个支持es6及以上语法的库。regenerator支持生成器语法。

## babel-runtime

babel-polyfill处理后的代码API名称不变，如果与另一个系统进行交互可能出现问题。babel-runtime能够给API起一个新名称。

## babel工作过程

babel是一个编译器，其工作过程可以分为三部分：

1. Parse(解析)

   解析经过词法分析、语法分析后生成抽象语法树。

2. Transform(转换)

   Babel对抽象语法树进行深度优先遍历，树的每个节点有个type属性，描述该节点的语法类型，Babel会用Visitor进行匹配，成功后调用Visitor中的方法，将es6语法转换为es5。遍历结束后会生成一颗新的基于es5语法的抽象语法树。

3. Generate(代码生成)