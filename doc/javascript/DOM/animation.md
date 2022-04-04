# 动画

## 定时器与延时器

定时器与延时器都是一种异步的程序执行方式。

> 异步(asynchronous) :不会阻塞CPu继续执行其他语句当异步完成时，会执行“回调函数”(callback)

### 定时器

**定时器创建**

setInterval()函数可以创建定时器，达到重复调用一个函数，在每次调用之间具有固定的时间间隔的效果。

setInterval函数第一参数是函数，第二个参数是间隔时间，单位为毫秒，可以接收多于2个参数，它们将按顺序传入函数。

```javascript
var timer = setInterval(function(a,b){
    //process
},1000,34,21);
```

**清除定时器**

clearInterval()函数可以清除一个定时器。需要传入一个定时器，该定时器由setInterval函数创建。

### 延迟器

**延迟器创建**

setTimeout()函数可以设置一个延时器，当指定时间到了之后，会执行函数一次，不再重复执行。

**延迟器清除**

clearTimeout(()函数可以清除延时器

## 动画实现

### 使用定时器实现动画

使用定时器可以实现动画，利用的就是“视觉暂留”原理。

**问题**

1. 不方便根据动画总时间计算步长
2. 运动方向要设置正负
3. 3多种运动进行叠加较为困难

### js和css3结合

JavaScript可以利用cSS3的transition属性轻松实现元素动画

JS和CSS3结合实现动画规避了定时器制作动画的缺点

#### 函数节流

一个函数执行一次后，只有大于设定的执行周期后才允许执行第二次的执行方式叫做函数节流。函数节流用于解决一个动画未完全播放完就开始另一个动画的问题。

示例代码

```javascript
var lock=false;
function Lock(processFunc){
    if(lock)return;
    processFunc();
    setTimeout(()=>lock=true,2000);
}
```

