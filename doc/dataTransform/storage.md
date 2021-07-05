# 本地存储

本地存储是将数据保存在浏览器的存储类别。本地存储有两种方式：Cookie和localStorage。

### Cookie

Cookie，全称是HTTP Cookie，存储的网页域名下的Cookie会随着该网页的请求而被浏览器发送到服务器。

Cookie主要用于记录用户在该网站的访问信息，比如，访问网页的时间、停留时间等。

#### Cookie属性

* 名称与值

  cookie的名称与名称对应的值是两个最重要的属性，必须填写。

  > cookie的名称或值中如果包含非英文字母，写入时需要使用encodeURIComponent()编码，读取时使用decodeURIComponent()解码

* 失效时间

  cookie记录的信息具有时效性，如果超过cookie声明的失效时间，那么这个cookie就会被浏览器清除。

  设置cookie失效时间有以下两种方式：

  1. expires

     在document.cookie赋值的字符串后添加expires键值对，用分号分隔。

     expires的值为Date类型。

  2. max-age

     max-age的值为数字，该数字表示cookie有效持续的秒数，如果该数字为0或负数，则cookie会被清除。

  > 如果一个cookie没有设置失效时间，则该cookie会在浏览器关闭时随之被清除，这样的cookie称为会话cookie。

* 域名 Domain

  Domain限定了在不同域名下cookie访问的范围。浏览器只能访问与Domain属性与当前网页域名相同的cookie。

  > js只能读取当前域与父域的cookie。

* 路径 Path

  Path属性先定了同一域名下cookie访问的范围。浏览器只能访问当前路径或当前路径之前的路径的cookie。

  > Path属性默认值是根目录。
  >
  > 如果两个cookie的name、domain、path都一样时，那么这两个cookie是同一个cookie，后赋值的cookie可以覆盖前者。

* HttpOnly

  设置了HttpOnly属性的cookie限定了只能浏览器访问，阻止js访问

* Secure

  Secure限定了只有在使用https的情况下才可以把cookie发送给服务器。

#### Cookie操作

* 读取

  通过document.cookie可以读取到网页的cookie，返回的是一个由键值对构成的字符串，其中用分号隔开

* 写入

  对document.cookie用“key=value”的形式赋值，可以将该键值对写入cookie，这个方式只能逐个写入。

### localStorage

localStorage是一种浏览器存储数据的方式，该数据只限定于浏览器读取，不会被发送到服务器。

#### localStorage用法

localStorage存储数据是键值对。

localStorage这个标识符在js中是一个对象，封装了操作localStorage的方法。

| 方法               | 说明                                           |
| ------------------ | ---------------------------------------------- |
| setItem(key,value) | 向localStorage中添加键值对数据                 |
| getItem(key)       | 从localStorage中获取给定键的值，不存在返回null |
| removeItem(key)    | 从localStorage中删除给定键                     |
| clear()            | 清除localStorage中所有数据                     |

#### 注意事项

* 键和值的类型

  localStorage存储的键值对类型必须是字符串，其余数据类型会转化成字符串

* 存储期限

  localStorage是持久化的本地存储，除非手动清除，永久不会过期

  > sessionStorage的存储期限是会话持续期间，当浏览器关闭时，sessionStorage中的数据会被清除