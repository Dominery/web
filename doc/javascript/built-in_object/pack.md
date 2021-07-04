# 包装类

Number()、String()和Boolean()分别是数字、字符串、布尔值的“包装类”

包装类的目的就是为了让基本类型值可以从它们的构造函数的prototype上获得方法

Number()、String()和Boolean()的实例都是object类型，它们的PrimitiveValue属性存储它们的本身值

new出来的基本类型值可以正常参与运算