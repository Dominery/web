# 解构赋值

解析某—数据的结构，将需要的数据提取出来，赋值给变量或常量的操作是解构赋值。

### 数组的解构赋值

#### 原理

1. 模式(结构)匹配
2. 索引值相同的完成赋值

#### 默认值

* 默认值的基本用法

  ```javascript
  const [a=0,b=1] = [];
  ```

* 默认值生效条件

  只有数组成员严格等于undefined，对应的默认值才会生效

* 默认值表达式

  如果默认值是表达式，默认值表达式是惰性求值的。

#### 应用

1. 常见类数组解构赋值，arguments，NodeList(返回的dom对象数组)

2. 函数参数的解构赋值

   ```javascript
   const add = ([x=0,y=0])=>x+y;
   ```

3. 交换变量值

### 对象的解构赋值

#### 原理

1. 模式匹配

2. 属性名相同的值完成赋值

   ```javascript
   const {age,username} = {username:"suyu",age:23};
   //完整的写法
   const {'age':age,'username':username} = {username:"suyu",age:23};
   //取别名
   const {age:age,username:uname} = {username:"suyu",age:23};
   ```

#### 注意事项

* 默认值通过等号设置，只有对象属性值严格等于undefined时，才会生效
* 如果将一个已经声明的变量用于解构赋值，整个赋值过程需要在圆括号内进行
* 可以获取到继承的属性

#### 应用

1. 函数参数的解构赋值
2. 嵌套对象

### 其他数据类型的解构赋值

#### 字符串

字符串既可以按数组形式来解构赋值，也可以按对象形式来解构赋值

* 数组形式的解构赋值

  ```javascript
  const [a,b] = "suyu";
  ```

* 对象形式解构赋值

  ```javascript
  const {0:a,1:b,length} = "suyu";
  ```

#### 数值和布尔值

只能使用对象形式来解构赋值，运行时会将等号右边的值转换为对象

#### undefined和null

由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错