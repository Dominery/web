# 内置对象

Math.round()可以将一个数字四舍五入为整数

Math .max()可以得到参数列表的最大值

Math.min()可以得到参数列表的最小值

Math.max()要求参数必须是“罗列出来”，而不能是数组。可以通过apply方法，以数组的形式传入“零散值”当做函数的参数

```javascript
Math.max.apply(null,[1,2,3,4,5]);
Math.max(1,2,3,4,5);
Math.max(...[1,2,3,4,5]);
```

Math.random()可以得到0~1之间的小数