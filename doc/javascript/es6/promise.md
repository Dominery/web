# Promise

Promise是异步操作的一种解决方案。

promise对象是对我们现在尚未得到但将来会得到值的占位符；它是对我们最终能够得知异步计算结果的一种保证。如果我们兑现了我们的承诺，那结果会得到一个值。如果发生了问题，结果则是一个错误，一个为什么不能交付的借口。

使用异步代码的原因在于不希望在执行长时间任务的时候，应用程序的执行被阻塞（影响用户体验）。

> 如果调用方需要等待被调用方运行结束，那么称这种方法调用为同步。
>
> 如果调用方不需等待被调用方运行结束，那么称这种方法调用为异步。

### 回调函数问题

没有Promise时，JavaScript异步操作通过回调函数实现。

回调函数存在着如下问题：

1. 异常难以捕获

2. 如果异步操作存在依赖，则会形成嵌套的回调函数，导致代码不利于维护，这种情况被称为“回调地狱”。

   > 如果要进行操作A，需要先完成操作B，则称A依赖于B，A和B存在依赖。

3. 回调函数难以协调多个并行的异步操作。

## 基本用法

### 实例化

可以通过构造函数Promise获取Promise对象，需要向Promise传入一个函数，该函数为执行函数，包含两个参数，resolve和reject。

### 状态

Promise有3种状态，分别是等待状态（pending）、完成状态（fulfiled）、拒绝状态（rejected）。

Promise对象创建时是等待状态；在程序执行时，如果resolve被调用，则promise进入完成状态；如果reject被调用，则promise进入拒绝状态。

Promise状态变化有如下规则：

* Promise的状态一旦变化，就不会改变。

* resolve和reject是改变状态的方法

  resolve和reject可以传递参数，then方法的回调函数可以接收到该参数。

### 实例方法

* then

  pending->fulfilled时，执行then的第一个回调函数；pending->rejected时，执行then的第二个回调函数。

  then方法执行后默认返回一个新的成功状态的Promise对象，如果需要返回特定的Promise对象需要实例化一个Promise对象并返回。

  在then的回调函数中,return的数据如果不是Promise，会默认用Promise包装。

  ```javascript
  new Promise(resolve=>{})
  .then(()=>{
      return "hello";
      /*equals to 
      return new Promise(resolve=>{resolve("hello")});
      */
  })
  ```

    > 当resolve 函数接收的是Promise对象时，后面的then 会根据传递的Promise 对象的状态变化决定执行哪一个回调

* catch

  catch可以传入一个回调函数用来专门处理rejected状态，相当于then(null,err=>{})。如果then中没有相应状态的回调函数，则此处的then不被执行，状态向下传递。

* finally

  finally接收一个回调函数，用于在Promise状态发生变化时执行。

  finally(handler)相当于then方法的如下表示：

    ```javascript
    .then{
        result=>{
            handler();
            return result;
        },
        err=>{
            handler();
            return new Promise((resolve,reject)=>{
                reject(err);
            })
        }
    }
    ```

### 构造函数方法

#### 单个Promise状态管理

* resolve

  成功状态Promise的一种简写形式。

   ```javascript
   new Promise(resolve=>resolve("success"));
   Promise.resolve("success");
   ```

  参数

  * 一般参数

  * Promise对象

    当Promise.resolve()接收Promise对象时，直接返回该对象

  * 具有then方法的对象

    Promise.resolve()会调用传入对象的then方法，并传入resolve和reject参数，如果对象then方法使用resolve或reject，Promise对象的状态会发生改变，否则不变。

* reject

  失败状态Promise的一种简写形式。

  不管什么参数，都会向后传递，作为后续方法的参数。

#### 多个Promise状态管理

* all

  promise.all可以传入多个Promise实例，包装成一个新的Promise实例返回，用来关注多个Promise的状态变化。

   Promise.all()的状态变化与所有传入的·Promise 实例对象状态有关。所有状态都变成resolved，最终的状态才会变成resolved；只要有一个变成 rejected，最终的状态就变成rejected。

* race

  Promise.race()的状态取决于第一个完成的Promise实例对象，如果第一个完成的成功了，那最终的状态就成功;如果第一个完成的失败了，那最终的状态就失败。

* allSettled

  Promise.allSettled()的状态与传入的Promise状态无关永远都是成功的，它只会记录下各个Promise的状态和值。

* any

  传入的参数是一组Promise实例，那么所有Promise实例都变成rejected状态，返回的Promise才会变成rejected状态，参数中只要有一个Promise改变为成功状态，则返回的Promise状态就是成功的状态。

### 注意事项

* resolve或reject执行后的代码依旧可以执行
* 处理多个Promise的Promise构造函数方法
  * 接收的参数需要是可迭代的
  * 传入的数组元素如果不是Promise会转变成Promise对象
  * 可以对错误进行统一处理

### 链式调用promise

promise可以链式调用，因而能够解决回调函数多重嵌套的问题。

当调用then方法后返回一个新的promise对象，可以在之后继续使用then方法处理该promise对象。

#### 错误捕获

promise链中错误捕获有两种方式

1. 通过then方法的第二个回调函数对特定步骤的错误进行处理
2. 使用catch方法会捕获所有之前的promise未处理的错误

## async/await

async/await是基于Promise实现的异步操作。

如果一个函数用async关键字修饰，则它会成为异步函数，返回一个Promise对象，可以使用then方法添加回调函数。当函数执行的时候，一旦遇到await就会先返回，等到触发的异步操作完成，再接着执行函数体内后面的语句。

### 注意事项

* await命令只能用在async函数之中，如果用在普通函数，就会报错。
* await后面跟着是一个Promise对象，会等待Promise返回结果了，再继续执行后面的代码。
* await后面跟着的是一个数值或者字符串等数据类型的值，则直接返回该值
* await后面跟着的是定时器，不会等待定时器里面的代码执行完，而是直接执行后面的代码，然后再执行定时器中的代码。

## 应用

### 数据校验

如果有一些信息需要进行数据校验，数据校验的操作存在一定的顺序，可以使用promise实现数据校验的功能。

如下为注册信息校验：

```javascript
function hasEmptyAttr (obj) {
  return Object.values(obj).some(value => value === '')
}
function validator(predict,message) {
  return data => new Promise((resolve, reject) => {
    if (predict(data)) {
      resolve(data)
    }
    reject(new Error(message))
  })
}
function emptyCheck (data) {
  return !hasEmptyAttr(data)
}
function confirmSame (data) {
  return data.password === data.confirm
}
async function submitData (data) {
  try {
    await register({ username: data.username, password: data.password })
  } catch (err) {
    return Promise.reject(new Error(getErrorMessage(err)))
  }
}
const registerClick = () => {
    const data = activeToObj(form)
    validator(emptyCheck,'has empty input')(data)
      .then(validator(confirmSame,'password not correspond'))
      .then(submitData)
      .then(successRegister)
      .catch(err => { registerInfo.value = err.message })
}
```

使用函数式实现注册校验：

```js
function compose (...fns) {
  return fns.reduce((fn1, fn2) => {
    return (...args) => {
      return fn1(fn2(...args))
    }
  })
}
/**
 * 如果校验函数从左到右依次对data进行校验，如果校验错误返回message
 * @param  {...any} validators 校验函数
 * @returns function 接收成功回调函数
 */
function checkerAll (...validators) {
  const funcChain = validators.map(validator => next => async data => {
    if (!validator(data)) return validator.message
    return await next(data)
  })
  return successHandler => {
    if (typeof successHandler !== 'function') throw Error('successHandler must be function')
    return compose(...funcChain)(successHandler)
  }
}

/**
 * 将谓词函数转变为验证函数
 * @param {string} message 错误消息
 * @param {function} fun 谓词函数
 * @returns function
 */
function validator (message, fun) {
  const f = (...args) => {
    return fun.apply(fun, args)
  }
  f.message = message
  return f
}
async function submitData (data) {
  try {
    await register({ username: data.username, password: data.password })
  } catch (err) {
    return Promise.reject(new Error(getErrorMessage(err)))
  }
}
function emptyCheck (data) {
  return !hasEmptyAttr(data)
}
function passwordConfirmCheck (data) {
  return data.password === data.confirm
}
const message = checkerAll(validator('has empty input', emptyCheck), validator('password not correspond', passwordConfirmCheck))(async data => {
      try {
        await submitData(data)
        return successHandler()
      } catch (err) {
        return err
      }
    })(data)
message.then(info => {
      registerInfo.value = info
    })
function successHandler () {
    setTimeout(() => {
      router.push({ name: 'Login' })
    }, 1000)
    return 'success to register'
}
```

### 示例代码

<promisifiedReadfile.js>文件

```javascript
const fs = require('fs');
// Below we create a function for reading files that returns a promise. We converted the fs.readfile() function which uses callbacks. Many of the asynchronous functions you'll encounter already return promises, so this extra step is seldom necessary. 
const promisifiedReadfile = (file, encoding) => 
  new Promise((resolve, reject) => {
    fs.readFile(file, encoding, (err, text) => {
			if (err) {
				return reject(err.message);
      }
        resolve(text);
      });
});

module.exports = promisifiedReadfile
```



```javascript
const fs = require('fs');
const promisifiedReadfile = require('./promisifiedReadfile');
      
// Here we use fs.readfile() and callback functions:
fs.readFile('./file.txt', 'utf-8', (err, data) => {
  if (err) throw err;
  let firstSentence = data;
  fs.readFile('./file2.txt',  'utf-8', (err, data) => {
    if (err) throw err;
    let secondSentence = data;
    console.log(firstSentence, secondSentence)
  });
});

// Here we use native promises with our "promisified" version of readfile:
let firstSentence
promisifiedReadfile('./file.txt', 'utf-8')
  .then((data) => {
    firstSentence = data;
    return promisifiedReadfile('./file2.txt', 'utf-8')
  })
  .then((data) => {
    let secondSentence = data;
    console.log(firstSentence, secondSentence)
  })
  .catch((err) => {console.log(err)});

// Here we use promisifiedReadfile() again but instead of using the native promise .then() syntax, we declare and invoke an async/await function:
async function readFiles() {
  let firstSentence = await promisifiedReadfile('./file.txt', 'utf-8')
  let secondSentence = await promisifiedReadfile('./file2.txt', 'utf-8')
  console.log(firstSentence, secondSentence)
}
readFiles()

```

