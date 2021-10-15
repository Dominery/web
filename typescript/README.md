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