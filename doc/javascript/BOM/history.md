# History对象

window.history对象提供了操作浏览器会话历史的接口

常用操作就是模拟浏览器回退按钮

```javascript
history.back();//等同于点击浏览器回退按钮
history.go(-1);//等同于history.back()
```

在html中用超级链接进行回退操作

```html
<a href="javascript:history.back();">回退</a>
```

