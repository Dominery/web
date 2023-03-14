# Node.js

#### 事件环

Node.js中有六个宏任务队列，依次执行，一个宏任务队列执行完会执行微任务队列。

process.nextTick()是Node.js中设置微任务的函数，优先级高于promise。

| 事件环            | 说明                            |
| ----------------- | ------------------------------- |
| timers            | 执行setTimeout与setInterval回调 |
| pending callbacks | 执行系统操作的回调，tcp udp     |
| idle， prepare    | 只在系统内部使用                |
| poll              | 执行与I/O相关的回调             |
| check             | 执行setImmediate回调            |
| close callbacks   | 执行close事件回调               |

执行同步代码，将不同的任务添加至相应的队列

所有同步代码执行后会去执行满足条件微任务

所有微任务代码执行后会执行timer队列中满足的宏任务

timer中的所有宏任务执行完成后就会依次切换队列

注意:在完成队列切换之前会先清空微任务代码

## 模块化

模块分为核心模块和文件模块。

核心模块是Node源码编译时写入二进制文件中，文件模块是在代码运行时动态加载。

### CommonJS

CommonJS规范起初是为了弥补JS语言模块化缺陷

CommonJS是语言层面的规范，当前主要用于Node.js

CommonJS规定模块化分为引入、定义、标识符三个部分

Moudle在任意模块中可直接使用包含模块信息

Require接收标识符，加载目标模块

Exports 与 module.exports 都能导出模块数据

模块加载是同步完成

### 模块对象

#### module

* 任意js文件就是一个模块，可以直接使用module属性
* id:返回模块标识符，一般是一个绝对路径
* filename:返回文件模块的绝对路径
* loaded:返回布尔值，表示模块是否完成加载
* parent:返回对象存放调用当前模块的模块
* children:返回数组，存放当前模块调用的其它模块
* exports:返回当前模块需要暴露的内容
* paths: 返回数组，存放不同目录下的node_modules

#### require

基本功能是读入并且执行一个模块文件

resolve:返回模块文件绝对路径

extensions:依据不同后缀名执行解析操作

main:返回主模块对象

### 加载流程

#### 路径分析

依据标识符确定模块位置

使用require()加载模块时会使用module.path路径数组依次查找模块路径是否存在。

**缓存优先原则**

提高模块加载速度

当前模块不存在，则经历一次完整加载流程模块

加载完成后，使用路径做为索引进行缓存

#### 文件定位

确定目标模块中具体的文件及文件类型

1. 项目下存在m1.js模块，导入时使用require(‘m1’)语法，查找m1.js → m1.json → m1.node

2. 找不到，在m1目录下查找package.json文件，使用JSON.parse()解析，取出main属性值，main.js → main.json → main.node
3. 找不到，将index做为目标模块中的具体文件名称
4. 使用路径数组查找

#### 编译执行

对模块内容进行编译，返回可用exports对象

**js 文件**

使用fs模块同步读入目标文件内容

对内容进行语法包装，生成可执行JS函数

调用函数时传入exports、module、require等属性值

**JSON文件**

将读取到的内容通过JSON.parse()进行解析

```javascript
function Module(id) {
    this.id = id;
    this.exports = {};
}

Module._cache = {};
Module._wrapper = function(content) {
    return `function(exports, require, module, __filename,__dirname){ ${content} }`;
}
Module._extensions = {
    '.js'(module){
        // 读取
        let content = fs.readFileSync(module.id, 'utf-8');
        // 包装
        content  = Module._wrapper(content);
        
        // 执行
        let compileFn = vm.runInThisContext(content);
        // 调用
        let exports = module.exports;
        const filename = module.id;
        const dirname = path.dirname(filename);
        
        compileFn.call(exports, exports, myRequire, module, filename, dirname);
        
    },
    '.json'(module){
        const content = JSON.parse(fs.readFileSync(module, 'utf-8'));
        module.exports = content;
    },
}
Module._resolveFilename = function (filename) {
    path.existSync(filename);
}

Module.prototype.load = function (){
    let extname = path.extname(this.id);
    
    Modle._extensions[extname](this);
    
}

function myRequire(filename) {
    // 1. 获取绝对路径
    let mPath = Module._resolveFilename(filename);
    
    // 2. 缓存优先
    let cacheModule = Module._cache[mPath];
    if (cacheModule) return cacheModule.exports;
    
    // 3. 创建对象加载目标模块
    let module = new Module(mPath);
    
    // 4. 缓存已加载过的模块
    Module._cahce[mPath] = module;
    
    // 5. 执行加载
    module.load();
    
    return module.exports;
}
```

## 核心模块

### VM

创建独立运行的沙箱环境。

```javascript
const vm = require('vm')

vm.runInThisContext('var age = 18');

console.log(age)
```

### path

用于处理文件/目录的路径。

| api          | 说明                   |
| ------------ | ---------------------- |
| basename()   | 获取路径中基础名称     |
| dirname()    | 获取路径中目录名称     |
| extname()    | 获取路径中拓展名称     |
| isAbsolute() | 获取路径是否为绝对路径 |
| join()       | 拼接路径片段           |
| resolve()    | 返回绝对路径           |
| parse()      | 解析路径               |
| format()     | 序列化路径             |
| normalize()  | 规范化路径             |

```javascript
path.resolve() // 返回当前工作目录路径 C:\code 
path.resolve('a', 'b') // C:\code\a\b
path.resolve('a', '/b') // C:\b
path.resolve('/a', 'b') // C:\a\b
path.resolve('/a', '/b') // C:\b
```

### Buffer

Buffer可以让JavaScript操作二进制数据。Buffer一般配合Stream使用，充当数据缓冲区。

> 数据生产者和数据消费者使用流借助管道通信，会存在等待情况，此时数据存放在Buffer中。

Nodejs中Buffer是一片内存空间，不占据V8内存，由Node控制，由V8的GC回收。

#### 创建实例

| 静态方法    | 说明                                           |
| ----------- | ---------------------------------------------- |
| alloc       | 创建指定字节大小的buffer                       |
| allocUnsafe | 创建指定字节大小的buffer，不会清除空间原有数据 |
| from        | 接收数据，创建buffer                           |

from方法可以接收字符串、数组、Buffer，第二个参数可以指明编码类型，如果传入数组，from会将数组中元素转换为2字节的数字，无法转换则为0。Buffer中文采用utf8编码占据3字节。

#### 实例方法

buffer可以使用下标访问。

| 实例方法                | 说明                                      |
| ----------------------- | ----------------------------------------- |
| fill(val, idx, end)     | [idx, end)范围内填充数据                  |
| write(val, idx, len)    | [idx, idx + len)写入数据                  |
| toString(str, idx, end) | 将[idx, end)buffer转换为str编码的字符串   |
| slice(idx, end)         | 截取指定范围[idx, end)数据                |
| indexOf(val, idx)       | 在buffer中从idx开始查找数据               |
| copy(buf1, i1, i2, e)   | 拷贝buffer中[i1, e)的数据到buf1的[i2, )中 |

#### 静态方法

| 静态方法                  | 说明                                      |
| ------------------------- | ----------------------------------------- |
| concat([buf1, buf2], len) | 将多个buffer拼接成一个长度为len的新buffer |
| isBuffer                  | 判断当前数据是否为buffer                  |

### FS

FS是内置核心模块，提供文件系统操作的API。

fd是操作系统分配给被打开文件的标识。

#### flag操作符

| flag | 含义             |
| ---- | ---------------- |
| r    | 表示可读         |
| w    | 表示可写         |
| s    | 表示同步         |
| +    | 表示执行相反操作 |
| x    | 表示排它操作     |
| a    | 表示追加操作     |

#### 文件操作API

| 方法         | 说明                             |
| ------------ | -------------------------------- |
| readFile()   | 从指定文件中读取数据             |
| writeFile()  | 向指定文件中写入数据             |
| appendFile() | 追加方式向指定文件中写入数据     |
| copyFile()   | 将某个文件中的数据拷贝到另一文件 |
| watchFile()  | 对指定文件进行监控               |

```javascript
const fs = require('fs');
const path = require('path');

fs.writeFile(path.resolve('data.txt'), 'hello world',{
    mode: 438, // 权限
    flag: 'r+', // r+ 不会清空原有文件
    encoding: 'utf-8',
} (err)=> {
    console.log(err);
})

fs.readFile(path.resolve('data.txt'), 'utf-8', (err, data)=>{
    if (!err) console.log(data);
});

fs.appendFile('data.txt', 'world', (err)=> {
    
})

fs.copyFile('data.txt', 'copy.txt', (err) => {});

fs.watchFile('data.txt', {
    interval: 20, // 监控间隔
}, (curr, prev) => {
    if (curr.mtime === prev.mtime) return;
    console.log('已修改');
    fs.unwatchFile('data.txt');
});

// 大文件读取，使用buffer作为缓冲区
let buf = Buffer.alloc(10);

fs.open('data.txt', 'r', (err, rfd) => {
    // 从文件第3字节开始读取数据，在buf的下标1开始写入4字节长度的数据
    fs.read(rfd, buf, 1, 4, 3, (err, readBytes, data) => {
        // readBytes 读取的数据的长度
    })
})

buf = Buffer.from('123456');
fs.open('w.txt', 'w', (err, wfd) => {
    // 在buf的下标1读取4字节长度的数据，从文件第3字节开始写入数据
    fs.write(wfd, buf, 1, 4, 3, (err, writeBytes, buffer) => {
        
    })
})
```

#### 目录操作API

| API     | 说明                         |
| ------- | ---------------------------- |
| access  | 判断文件或目录是否有操作权限 |
| stat    | 获取目录及文件信息           |
| mkdir   | 创建目录                     |
| rmdir   | 删除目录                     |
| readdir | 读取目录中内容               |
| unlink  | 删除指定文件                 |

```javascript
const fs = require('fs');
fs.access('a.txt', (err) => {
    if (err) {
        
    }else {
        console.log('有权限')
    }
});

fs.stat('a.txt', (err, statObj) => {
    console.log(statObj)
});

fs.mkdir('a/b/c', {revursive: true}, (err)=>{})
```

### Events

通过 EventEmitter类实现事件统一管理

node.js是基于事件驱动的异步操作架构，内置events模块

events模块提供了EventEmitter类

node.js中很多内置核心模块继承 EventEmitter

| API  | 说明                               |
| ---- | ---------------------------------- |
| on   | 添加事件触发时的回调函数           |
| emit | 触发事件，按照注册顺序调用回调函数 |
| once | 添加事件首次触发时执行的函数       |
| off  | 移除特定的监听器                   |

### Stream

同步读取资源文件，存在如下问题

1. 用户需要等待数据读取完成资源文件
2. 文件数据加载至内存，开销较大

Node.js中的流就是处理流式数据的抽象接口。Stream模块实现四个具体的抽象，所有流都继承自EventEmitter。

**流的优势**

时间效率:流的分段处理可以同时操作多个数据chunk

空间效率:同一时间流无须占据大内存空间

使用方便:流配合管理，扩展程序变得简单

**流的分类**

Readable:可读流，能够实现数据的读取

Writeable:可写流，能够实现数据的写操作

Duplex:双工流，既可读又可写

Tranform:转换流，可读可写，还能实现数据转换

```javascript
const fs = require('fs');

const rs = fs.createReadStream('./big_data.txt');
const ws = fs.createWriteStream('./copy.text', {
    flags: 'w',
    mode: 438,
    fd: null,
    encoding: 'utf-8',
    start: 0,
    highWaterMark: 3,
})
rs.pipe(ws);
```

#### 可读流

自定义可读流

1. 继承stream里的Readable
2. 重写_read方法调用push产出数据，当数据读取完给push传递null

消费数据：

* readable事件：当流中存在可读取数据时触发
* data事件：当流中数据块传给消费者后触发
* end事件：当流传递完所有数据触发

```javascript
const { Readable } = require('stream');

class MyReadable extends Readable{
    constructor(source) {
        super()
        this.source = source;
    }
    
    _read() {
        let data = this.source.shift() || null;
        this.push(data);
    }
}

let myR = new MyReadable(['12','34','56']);

myR.on('readable', () => {
    let data = null;
    while ((data = myR.read()) != null) {
        console.log(data.toString());
    }
})

myR.on('data', chunk => {
    console.log(chunk.toString());
})
```

#### 可写流

事件

* pipe事件：可读流调用pipe()方法触发
* unpipe事件：可读流调用unpipe()方法触发

write方法

1. 第一次调用write方法时是将数据直接写入到文件中
2. 第二次开始write方法就是将数据写入至缓存中
3. 生产速度和消费速度是不一样的，一般情况下生产速度要比消费速度快很多
4. 当write返回值为false之后并不意味着当前次的数据不能被写入了但是我们应该告之数据的生产者，当前的消费速度已经跟不上生产速度了，所以这个时候，一般我们会将可读流的模式修改为暂停模式。
5. 当数据生产者暂停之后，消费者会慢慢的消化它内部缓存中的数据，直到可以再次被执行写入操作
6. 当缓存区可以再次写入数据消费者会触发drain事件

如果缓存区写满，依旧调用write方法，可能会导致内存溢出、GC频繁调用、其他进程变慢等问题。

```javascript
const { Writable } = require('stream');

class MyWritable extends Writable {
    _write(chunk, en, cb) {
        process.stdout.write(chunk.toString());
        process.nextTick(cb);
    }
}

let mw = new MyWritable();
mw.write('hello', 'utf-8', () => {
    console.log('end')
})
```

#### 双工流

双工流既能生产又能消费

自定义

1. 继承 Duplex类

2. 重写_read方法，调用push生产数据

3. 重写_write方法,调用write消费数据

```javascript
class MyDuplex extends Duplex {
    constructor(source) {
        super();
        this.source = source;
    }
    _read() {
        this.push(this.source.shift() || null);
    }
    _write(chunk, en, next) {
        process.stdout.write(chunk);
        process.nextTick(next);
    }
}
```

#### 转换流

转换流中是特殊的双工流，读写操作存在联系。

自定义

1. 继承 Transform类

2. 重写_transform方法，调用push和 callback

3. 重写_flush方法，处理剩余数据

#### 背压机制

背压机制协调读写速度，当可写流缓存空间使用完后暂停可读流读入，直至可写流缓存空间恢复。

```javascript
const rs = fs.createReadStream('data.txt', {
    highWaterMark: 6,
})
const ws = fs.createWriteStream('copy.txt', {
    highWaterMark: 3,
})
let falg = true;
rs.on('data', (chunk) => {
    flag = ws.write(chunk);
    if (!flag) rs.pause();
})

ws.on('drain', () => {
    rs.resume();
})
```



