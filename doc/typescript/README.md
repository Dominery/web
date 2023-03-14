# TypeScript

TypeScript是JavaScript的超集，基于ECMAScript语法，还具有强类型、接口、泛型等拓展功能。TypeScript无法在浏览器中运行，需要借助编译器编译成JavaScript。

TypeScript具有类型推演、类型匹配的功能，全面兼容JavaScript语法特性，能够规范代码，暴露编译错误。

## 开发环境配置

### 安装typescript

1. 通过npm全局安装

   ```
   npm install -g typescript
   ```

2. 输入`tsc -v`如果显示typescript版本号，则安装成功

### 编译

安装typescript后有两种方法编译typescript代码。

* 通过全局命令`tsc`编译typescript文件。

* 工程化编译，使用node管理
  1. 在工程目录创建src，在src目录下创建index.ts文件
  2. 将package.json中入口文件改为`src/index.ts`
  3. 运行`tsc --init`，会创建`tsconfig.json`文件
  4. 配置`tsconfig.json`文件
  5. 在pakage.json中添加运行命令`"tsc":"tsc"`
  6. 下载开发依赖包`@types/node`、`typescript`
  7. 通过`npm run tsc`编译所有typescript文件

## 类型系统

### 类型安全

从类型安全的角度，编程语言分为强类型和弱类型语言。在语言层面限制函数的实参类型和形参类型必须相同。弱类型不会限制实参类型。

强类型不允许有数据隐式类型转换，弱类型允许数据隐式类型转换。

强类型优势

* 错误更早暴露
* 代码更智能，编码更准确
* 重构更牢靠
* 减少不必要的类型判断

#### Flow

JavaScript的类型检查器。通过在代码中添加类型注解的方式，Flow判断代码运行时是否有类型错误。

**移除类型注解**

1. flow-remove-types
2. @babel/core、@babel/cli、@babel/preset-flow