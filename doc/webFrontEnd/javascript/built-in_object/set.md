# Set

Set是一系列无序、没有重复值的数据集合。Set可以用于数组或字符串去重。

### 创建

Set通过构造函数创建。Set只有一个size属性。

将容器对象传入Set的构造函数，会创建一个Set，遍历容器对象，将元素添加到Set中后返回。

> 容器对象包括：数组、字符串、arguments、NodeList、Set

### 方法

| 方法    | 说明                                           |
| ------- | ---------------------------------------------- |
| add     | 添加一个元素，返回set对象                      |
| delete  | 删除元素，如果不存在，没有信息提示             |
| clear   | 删除全部元素                                   |
| forEach | 按添加顺序遍历元素，传入回调函数和函数的上下文 |

* forEach的回调函数

  回调函数三个参数分别代表元素值，键，容器本身。该回调函数在所有forEach方法中形式统一。

Set判断重复方式

Set 对重复值的判断基本遵循严格相等(===)，但是对于NaN的判断与=\=\=不同，Set 中NaN等于NaN
