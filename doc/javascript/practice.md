# 实践

## 责任链模式

### 表单校验

```javascript
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
function checkerAll (validators) {
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

function validatorsGen(checks) {
    return checks.map(({validate, message}) =>validator(message, validate));
}
```

使用示例

```javascript
async function submitData (data) {
  try {
    await register({ username: data.username, password: data.password });
    return successHandler();
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

const checkSequence = [{
    validate: emptyCheck,
    message: 'has empty input',
}, {
    validate: passwordConfirmCheck,
    message: 'password not correspond',
}];

const message = checkerAll(validatorsGen(checkSequence))(submitData)(data)
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

## 异步

### 异步池

```javascript
async function asyncPool(generator, limit = 5) {
  const pool = new Set();
  const res = [];
  let index = 0;
  for (let task of generator) {
    let pos = index++;
    const wrappedTask = task.then(result => {
      res[pos] = {status: 'fulfiled', result};
    }, reason => {
        res[pos] = {status: 'rejected', reason};
    }).finally(() => {
      pool.delete(wrappedTask);
    });
    pool.add(wrappedTask);
    if (pool.size === limit) {
      await Promise.race(pool);
    }
  }
  await Promise.all(pool);
  return  res;
}
```

使用方式

```javascript
function *gen() {
  for(let i =0;i<10;i++){
    yield new Promise((resolve,reject)=>{
      const delay = Math.floor(Math.random() * 1000);
      setTimeout(() => {
        if(Math.random()>.5)resolve([i,delay]);
        else reject([i,delay]);
      }, delay);
    })
  }
}
asyncPool(gen())
```

### 轮询

**直接执行**

```javascript
function pollGen(errHandler, wait = 500) {
    
  return async function polling(request) {
    let isSuccess = false;
    try {
      isSuccess = await request();
    } catch (err) {
      errHandler(err);
    }
    if (isSuccess) {
      return
    } 
    setTimeout(() => {
      polling(request);
    }, wait);
  }
}
```

**延后执行**

```javascript
function pollGenLater(errHandler, wait = 500) {
  
  return function polling(request) {
    setTimeout(async() => {
      let isSuccess = false;
      try {
        isSuccess = await request();
      } catch (err) {
        errHandler(err);
      }
      if (isSuccess) {
        return
      }
      polling(request);
    }, wait);
  }
}
```

**promise**

```javascript
function pollGen(request, wait = 500, ...args) {
  return new Promise(async (resolve, reject) => {
    let isSuccess = false;
    try {
      isSuccess = await request(...args);
    } catch (err) {
      return reject(err);
    }
    if (isSuccess) return resolve();
    
    setTimeout(() => {
      resolve(pollGen(request, wait, ...args));
    }, wait);
  })
}
```

## 对象

### 深拷贝

1.  普通对象、数组、基本类型除undefined外

   使用JSON.parse()和JSON.stringfy()。

2.  需要拷贝类型不符合JSON规范

    使用Object.keys()获取属于对象自身的、可枚举的、不是Symbol属性的属性名数组。

3.  拷贝的对象深度超过递归深度

4. 存在Function、Regex、Date对象

   使用instanceof 进行判断，再分别用各自的构造实例化

5. 存在用户类实例化对象

   使用Object.getPrototypeOf()获取原型，通过Obejct.create()根据原型创建对象

6. 存在循环引用

   使用一个weakMap记录已经拷贝的值，如果下次拷贝时，map有值则直接返回

7. 需要拷贝不可枚举属性

   使用Object.getOwnPropertyNames()获取到对象自身的、包含不可枚举、不包含Symbol属性。

8. 需要拷贝Symbol属性

   使用Reflect.Ownkeys()获取到所有属于对象自身的属性。

9. 需要拷贝getter、setter

   1. 使用Object.getOwnPropertyDescriptor()获取属性描述对象，Object.defineProperty()定义属性描述对象。
   2. 使用Object.getOwnPropertyDescriptors()获取所有对象自身的属性描述对象，Object.defineProperties()设置多个属性描述对象

```javascript
function getType(value) {
    return Object.prototype.toString.call(value).slice(8, -1).toLowerCase();
}

const baseType = new Set(['number', 'string', 'null', 'undefined', 'bigint', 'symbol', 'boolean']);

function deepClone(value,cache=new WeakMap()){
    if(baseType.has(getType(value))) return value;
    if(cache.get(value)) return cache.get(value);
    
    if(value instanceof Date) return new Date(value);
    if(value instanceof RegExp) return new RegExp(value.source,value.flags);
    if(value instanceof Function) return new Function(value);

    const result = Array.isArray(value) ? []:Object.create(Obejct.getPrototypeOf(value));
    cache.set(value,result);
    
    for(let key of Reflect.ownKeys(value)) {
        const descriptorOld = Object.getOwnPropertyDescriptor(key);
        const descriptorValue = descriptorOld.value;
        const descriptor = descriptorValue ? {...descriptorOld, value: deepClone(descriptorValue, cache)} : descriptorOld;// 处理get、set，如果本身无value属性，不能添加value
        Object.defineProperty(result, key, descriptor);
    }
    
    return result;
}
```

白屏时间

window.performance.timing.domLoading - window.performance.timing.navigationStart

首屏时间

window.performance.timing.domInteractive - window.performance.timing.navigationStart