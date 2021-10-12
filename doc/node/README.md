# Nodejs

nodejs是一个基于ChromeV8引擎的Javascript运行时。

> V8是chrome的js引擎，可以执行js代码
>
> 运行时是代码的运行环境

nodejs出现以前，只有浏览器才能执行js代码

**nodejs与前端js**

nodejs与前端js使用的都是js语言，但是两者提供的API不同。在nodejs环境下，无法使用如window、dom等webAPI。

前端js=js语法+Web API；nodejs = js语法 + nodejs API。

## npm

npm，全称node package manager，是nodejs软件包管理器。[官网](https://www.npmjs.com/)中能够搜索到各种软件包。

### 命令

| 命令                        | 说明                               |
| --------------------------- | ---------------------------------- |
| npm init                    | 初始化项目目录                     |
| npm install                 | 安装package里的依赖项              |
| npm i lodash                | 安装lodash                         |
| npm update                  | 更新所有软件包，可以指定单个软件包 |
| npm run [task-name]         | 指定命令行任务运行                 |
| npm list                    | 查看所有软件包及其依赖包的信息     |
| npm view [package] versions | 查看指定软件包所有以前的版本       |
| npm uninstall [package]     | 卸载指定软件包                     |

#### 镜像源

因为npm存在安装软件包速度慢的问题，可以通过npm全局安装nrm，nrm能够设置npm的镜像源，加快软件包安装速度。

| 命令           | 说明             |
| -------------- | ---------------- |
| nrm ls         | 显示所有的镜像源 |
| nrm use taobao | 使用taobao镜像源 |

#### 安装

npm安装软件包有两种安装方式，本地安装和全局安装，它们安装软件包的路径不同。

当使用`npm install [package-name]`时，软件包会被安装在项目目录的node_modules文件夹中。

**参数**

npm安装时可以通过参数指定依赖项的使用方式。

* --save

  如果在代码中需要使用依赖项，那么要指定--save。依赖项信息会被添加到package的dependencies中

* -dev

  如果依赖项仅在开发环境使用，那么要指定-dev。依赖项信息会被添加到package的devDependencies中

npm安装时可以使用@语法指定安装依赖项的版本。如`npm install webpack@1.2.0`。

如果在安装命令中加-g参数，则软件包会被安装在全局位置中，可以使用`npm root -g`获知具体位置。

#### 卸载

因为软件包的安装方式不同，卸载软件包可以根据卸载需求设置相应参数。

卸载全局依赖时要使用-g。

卸载本地软件包时，如果要移除在package中的引用，通过-S或--save从dependencies移除，通过-D或者--save-dev从devDependencies中移除。

### 配置文件

项目目录中会有两个配置文件，package.json和pacakge-lock.json。

package.json记录当前项目依赖软件包的版本信息。package-lock.json中记录项目中使用所有依赖的信息，包括来源、版本号等。package-lock中记录的依赖要多于package。

#### 项目命令

package.json中scripts存储该项目可以运行的指令，需要通过在命令行输入`npm run [instraction]`运行。

## commonjs

comonjs和ES6 Module都是JavaScript的模块系统。

### commonjs和ES6 Module的区别

commonjs是执行时引入，可以将模块导入语句放在任何位置，这种特性称为动态；ES6 Module是打包时引入，只能在全局作用域且最顶层的位置书写模块导入语句，这种特性称为静态。

require三个层次

* 系统自带模块
* npm包
* 自定义模块

## debug

如果想要对提供后端服务代码进行debug，那么可以使用inspect调试法。

### inspect调试法

* 修改package内的scripts，增加--inspect
* 在想要调试的地方增加debugger关键字，重启服务
* 打开chrome，访问chrome://inspect，进入target的inspect
* 新开chrome标签页，在上面输入请求的url
* 进入inspect界面，inspect上的js代码会停留在debugger位置