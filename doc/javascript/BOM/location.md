# Location对象

location对象既是window的属性，也是document的属性。

## 属性

| 属性     | 说明                  |
| -------- | --------------------- |
| hash     | URL散列值，井号后字符 |
| host     | 服务器名及端口号      |
| hostname | 服务器名              |
| href     | 当前页面URL           |
| pathname | URL中路径名           |
| port     | 端口                  |
| protocol | 使用协议              |
| search   | URL查询字符           |
| origin   | URL源地址             |

URLSearchParams构造函数能够解析URL查询字符。location属性都可以赋值修改，除了hash属性，其余属性修改后都会导致页面重新加载。

## 地址修改

* location.assign()
* 对`location.href`、`window.location`赋值，效果与location.assign()相同
* location.replace()，该方法不会在浏览器历史记录中增加记录，无法回退
* location.reload()，重新加载当前页面，如果传入参数true，强制从服务器强制加载