# Module模块

模块是一个个局部作用域中的代码块。模块系统能够管理多个模块使之能够协同工作。

模块系统解决的主要问题：

* 模块化问题
* 消除全局变量
* 管理加载顺序

## ES6模块系统

ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。比如，CommonJS 模块就是对象，输入时必须查找对象属性。

ES6 模块不是对象，而是通过`export`命令显式指定输出的代码，再通过`import`命令输入。这种加载称为“编译时加载”或者静态加载，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高，使得静态分析成为可能。

模块功能主要由两个命令构成：`export`和`import`。`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。

### export 命令

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用`export`关键字输出该变量。

```javascript
export const name = 'lihua';
export {};
```

`export`命令除了输出变量，还可以输出函数或类。`export`输出的变量就是本来的名字，但是可以使用`as`关键字重命名。

* `export`命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系。
* `export`语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。
* `export`命令可以出现在模块的任何位置，只要处于模块顶层就可以。

#### 复合写法

如果在一个模块之中，先输入后输出同一个模块，`import`语句可以与`export`语句写在一起。

```javascript
export { foo, bar } from 'my_module';

// 可以简单理解为
import { foo, bar } from 'my_module';
export { foo, bar };
```

写成一行以后，`foo`和`bar`实际上并没有被导入当前模块，只是相当于对外转发了这两个接口，导致当前模块不能直接使用`foo`和`bar`。

默认接口的写法如下。

```javascript
export { default } from 'foo';
```

具名接口改为默认接口的写法如下。

```javascript
export { es6 as default } from './someModule';

// 等同于
import { es6 } from './someModule';
export default es6;
```

同样地，默认接口也可以改名为具名接口。

```javascript
export { default as es6 } from './someModule';
```

ES2020增加下面的复合写法。

```javascript
export * as ns from "mod";
```

`export *`命令会忽略模块的`default`方法。

### import 命令

使用`export`命令定义了模块的对外接口以后，其他 JS 文件就可以通过`import`命令加载这个模块。

`import`命令接受一对大括号，里面指定要从其他模块导入的变量名。大括号里面的变量名，必须与被导入模块对外接口的名称相同。

* 如果想为输入的变量重新取一个名字，`import`命令要使用`as`关键字，将输入的变量重命名。
* `import`命令输入的变量都是只读的，因为它的本质是输入接口。
* `import`命令具有提升效果，会提升到整个模块的头部，首先执行。
* 由于`import`是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。
* `import`语句会执行所加载的模块
* 被导入的代码会执行且仅执行一遍。

#### 模块整体加载

除了指定加载某个输出值，还可以使用整体加载，即用星号（`*`）指定一个对象，所有输出值都加载在这个对象上面。

```javascript
import * as Util from './util'
```

模块整体加载所在的那个对象，应该是可以静态分析的，所以不允许运行时改变。

#### import.meta

开发者使用一个模块时，有时需要知道模板本身的一些信息（比如模块的路径）。[ES2020](https://github.com/tc39/proposal-import-meta) 为 import 命令添加了一个元属性`import.meta`，返回当前模块的元信息。

`import.meta`只能在模块内部使用，如果在模块外部使用会报错。这个属性返回一个对象，该对象的各种属性就是当前运行的脚本的元信息。

**import.meta.url**

`import.meta.url`返回当前模块的 URL 路径。

Node.js 环境中，`import.meta.url`返回的总是本地路径。

**import.meta.scriptElement**

`import.meta.scriptElement`是浏览器特有的元属性，返回加载模块的那个`<script>`元素，相当于`document.currentScript`属性。

### default命令

使用`import`命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到`export default`命令，为模块指定默认输出。

其他模块加载该模块时，`import`命令可以为该匿名函数指定任意名字。

`export default`命令用在非匿名函数前，也是可以的。

因为`export default`命令的本质是将后面的值，赋给`default`变量，导出的是一个值，而非引用，所以它后面不能跟变量声明语句，可以直接将一个值写在`export default`之后。

```javascript
// modules.js
function add(x, y) {
  return x * y;
}
export {add as default};
// 等同于
// export default add;

// app.js
import { default as foo } from 'modules';
// 等同于
// import foo from 'modules';
```

如果想在一条`import`语句中，同时输入默认方法和其他接口，可以写成下面这样。

```javascript
import _, { each, forEach } from 'lodash';
```

### import()

`import`命令会被 JavaScript 引擎静态分析，先于模块内的其他语句执行，导致无法在运行时加载模块。在语法上，条件加载就不可能实现。

ES2020引入`import()`函数，支持动态加载模块。

`import`函数的参数`specifier`，指定所要加载的模块的位置。返回一个 Promise 对象。

`import()`加载模块成功以后，这个模块会作为一个对象，当作`then`方法的参数。

## Module加载实现

### 浏览器加载

浏览器加载 ES6 模块，也使用`<script>`标签，但是要加入`type="module"`属性。

浏览器对于带有`type="module"`的`<script>`，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了`<script>`标签的`defer`属性。

`<script>`标签的async属性也可以打开，这时只要加载完成，渲染引擎就会中断渲染立即执行。执行完成后，再恢复渲染。

- 代码是在模块作用域之中运行，而不是在全局作用域运行。模块内部的顶层变量，外部不可见。
- 模块脚本自动采用严格模式，不管有没有声明`use strict`。
- 代码是在模块作用域之中运行，而不是在全局作用域运行。模块内部的顶层变量，外部不可见。
- 模块脚本自动采用严格模式，不管有没有声明`use strict`。
- 同一个模块如果加载多次，将只执行一次。
- 通过CORS去请求外部JS模块的

### 与Commonjs差异

 ES6 模块与 CommonJS 模块完全不同。

它们有三个重大差异。

- CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用，export default输出的是一个值。
- CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。
- CommonJS 模块的`require()`是同步加载模块，ES6 模块的`import`命令是异步加载，有一个独立的模块依赖的解析阶段。
- CommonJs在第一次加载的时候运行一次并且会生成一个缓存,之后加载返回的都是缓存中的内容

CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。ES6 模块的运行机制与 CommonJS 不一样。JS 引擎对脚本静态分析的时候，遇到模块加载命令`import`，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。

### Node.js加载

JavaScript 现在有两种模块。一种是 ES6 模块，简称 ESM；另一种是 CommonJS 模块，简称 CJS。

CommonJS 模块是 Node.js 专用的，与 ES6 模块不兼容。语法上面，两者最明显的差异是，CommonJS 模块使用`require()`和`module.exports`，ES6 模块使用`import`和`export`。

Node.js 要求 ES6 模块采用`.mjs`后缀文件名。如果不希望将后缀名改成`.mjs`，可以在项目的`package.json`文件中，指定`type`字段为`module`。如果这时还要使用 CommonJS 模块，那么需要将 CommonJS 脚本的后缀名都改成`.cjs`。

`.mjs`文件总是以 ES6 模块加载，`.cjs`文件总是以 CommonJS 模块加载，`.js`文件的加载取决于`package.json`里面`type`字段的设置。

#### package.json

`package.json`文件有两个字段可以指定模块的入口文件：`main`和`exports`。

##### main字段

```javascript
// ./node_modules/es-module-package/package.json
{
  "type": "module",
  "main": "./src/index.js"
}
```

上面代码指定项目的入口脚本为`./src/index.js`，它的格式为 ES6 模块。如果没有`type`字段，`index.js`就会被解释为 CommonJS 模块。

##### exports字段

`exports`字段的优先级高于`main`字段。

**子目录别名**

`package.json`文件的`exports`字段可以指定脚本或子目录的别名。

```javascript
// ./node_modules/es-module-package/package.json
{
  "exports": {
    "./submodule": "./src/submodule.js"
  }
}
```

上面的代码指定`src/submodule.js`别名为`submodule`，然后就可以从别名加载这个文件。

```javascript
import submodule from 'es-module-package/submodule';
// 加载 ./node_modules/es-module-package/src/submodule.js
```

如果没有指定别名，就不能用“模块+脚本名”这种形式加载脚本。

**main的别名**

`exports`字段的别名如果是`.`，就代表模块的主入口，优先级高于`main`字段，并且可以直接简写成`exports`字段的值。

```javascript
{
  "exports": {
    ".": "./main.js"
  }
}

// 等同于
{
  "exports": "./main.js"
}
```

**条件加载**

利用`.`这个别名，可以为 ES6 模块和 CommonJS 指定不同的入口。目前，这个功能需要在 Node.js 运行的时候，打开`--experimental-conditional-exports`标志。

```javascript
{
  "type": "module",
  "exports": {
    ".": {
      "require": "./main.cjs",
      "default": "./main.js"
    }
  }
}
```

#### Commonjs 加载ES6模块

CommonJS 的`require()`命令不能加载 ES6 模块，会报错，只能使用`import()`这个方法加载。

`require()`不支持 ES6 模块的一个原因是，它是同步加载，而 ES6 模块内部可以使用顶层`await`命令，导致无法被同步加载。

#### ES6 模块加载Commonjs

ES6 模块的`import`命令可以加载 CommonJS 模块，但是只能整体加载，不能只加载单一的输出项。

这是因为 ES6 模块需要支持静态代码分析，而 CommonJS 模块的输出接口是`module.exports`，是一个对象，无法被静态分析，所以只能整体加载。

##### Node.js 的内置模块

Node.js 的内置模块可以整体加载，也可以加载指定的输出项。

##### 加载路径

ES6 模块的加载路径必须给出脚本的完整路径，不能省略脚本的后缀名。`import`命令和`package.json`文件的`main`字段如果省略脚本的后缀名，会报错。

同一个脚本只要参数不同，就会被加载多次，并且保存成不同的缓存。由于这个原因，只要文件名中含有`:`、`%`、`#`、`?`等特殊字符，最好对这些字符进行转义。

* Node.js 的`import`命令只支持加载本地模块（`file:`协议）和`data:`协议，不支持加载远程模块。
* 脚本路径只支持相对路径，不支持绝对路径