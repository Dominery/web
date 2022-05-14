# 文件模块



## fs模块

fs模块是Node.js提供的用于操作文件的模块。

### promise形式

fs模块增加了promise形式的调用，简单的使用方式如下：

```js
const {readFile} = require("fs").promises
const demoPath = path.resolve(__dirname,"../package.json")

readFile(demoPath,"utf-8")
.then(value=>{
  console.log(value)
})
```

## path路径模块

### 方法

| 方法            | 说明                           |
| --------------- | ------------------------------ |
| path.join()     | 将多个路径片段拼接为完整的路径 |
| path.basename() | 获取路径的最后一部分（文件名） |
| path.extname()  | 获取路径文件的拓展名           |

