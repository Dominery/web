# DOM

DOM (Document Object Model，文档对象模型）是JavaScript操作HTML文档的接口，使文档操作变得非常优雅、简便。

DOM最大的特点就是将文档表示为节点树。

### nodeType

节点的nodeType属性可以显示这个节点具体的类型。

| nodeType值 | 节点类型     |
| ---------- | ------------ |
| 1          | 元素节点     |
| 3          | 文字节点     |
| 8          | 注释节点     |
| 9          | document节点 |
| 10         | DTD节点      |

### 延迟运行

在测试DOM代码时，JS代码需要书写在html后面，否则DOM还没有建立完成。可以使用window.onload = function()事件，使页面加载完毕后，再执行指定的代码