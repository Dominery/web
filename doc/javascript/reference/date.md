# Date对象

### 创建

1. new Date()

   使用new Date()即可得到当前时间的日期对象，它是object类型值

2. new Date(milliseconds)

   创建一个Date对象，时间等于1970.1.1UTC+0经过的毫秒数

3. new Date(datestring)

   与Date.parse所使用的算法相同

4. new Date(year,month,date,hours,minutes,secondes,ms)

   month从0开始计数

### 常见方法

| 方法          | 说明           |
| ------------- | -------------- |
| getDate()     | 得到日期1~31   |
| getDay()      | 得到星期0~6    |
| getMonth()    | 得到月份0~11   |
| getFullYear() | 得到年份       |
| getHours()    | 得到小时数0~23 |
| getMinutes()  | 得到分钟数0~59 |
| getSeconds () | 得到秒数0~59   |

每个get方法都有对应的set方法，用来修改日期对象。Date对象有自动校准的特性，对于超出范围的数值，会进行校准。

### 时间戳

时间戳表示1970年1月1日零点整距离某时刻的毫秒数。Date精度仅支持到毫秒，更高精度的数值需要通过其他方法获取，如浏览器提供performance.now()方法获取页面加载开始以毫秒为单位的微秒数。

#### 创建方法

在日期对象前使用一元运算符`+`可以将其强制类型转换为数字，返回结果为Unix时间戳。

1. getTime()

   调用日期对象的getTime()方法可以得到日期对象对应的时间戳，精确到毫秒

2. Date.parse()

   字符串的格式为：`YYYY-MM-DDTHH:mm:ss.sssZ`
   
   - `YYYY-MM-DD` —— 日期：年-月-日。
   - 字符 `"T"` 是一个分隔符。
   - `HH:mm:ss.sss` —— 时间：小时，分钟，秒，毫秒。
   - 可选字符 `'Z'` 为 `+-hh:mm` 格式的时区。单个字符 `Z` 代表 UTC+0 时区。
   
3. Date.now()

