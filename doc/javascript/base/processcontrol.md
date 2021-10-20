# 流程控制

## 条件语句

#### if语句

if语句是最简单的条件语句，也称选择语句。if语句可以使用else if 进行多条件判断，else if条件分支“暗含”不符合之前所有条件。

```javascript
if(condition1){
    // if condition1 is true run this area
}else if(condition2){
    // if condition1 is false and condition2 is true
}else{
    // if condition1 and condition2 both are false
}
```

* 单行if语句

  如果if语句体中只有一行语句，可以省略大括号和换行

#### switch语句

switch语句的用途:当一个变量被分类讨论的情形。在switch()的圆括号中一般是一个变量名，这个变量将被分类讨论。

```javascript
switch(variable){
    case value1:
        //process
        break;
    case value2:
        //process
        break;
    default:
        //process
}
```

* case

  case表示“情况”，它后面没有圆括号，直接跟一个值。

  程序会依次将case后面的值与switch圆括号中的值进行**全等**比对，如果比对相同，则执行这条case冒号后面的语句。default表示默认情况。

* break

  switch语句并不会执行了某一个分支之后自动语句体，程序员必须主动调用break来跳出switch语句体。如果不书写break，则后面的所有case都将被视为匹配，直到遇见break。

#### 三元运算符

三元运算符的用途:根据某个条件是否成立，在两个不同值中选择变量的值。

> 条件表达式?表达式1:表达式2
>
> 问号前面是判断的条件，问号后面用冒号隔开两个表达式。当条件表达式为真时调用表达式1，为假时调用表达式2。

## 循环语句

#### for循环

for的圆括号中有三个表达式:
表达式var i = 1;表示定义一个“循环变量”i，并赋值为1;
表达式i <= 10;表示继续执行循环的条件，只要这个条件为真，则会一直执行;表达式i++用来更新循环变量，使循环变量的值越来越趋向终点。

```javascript
outer:for(var i = 1;i<100;i++){
    console.log(i);
    for(var j=0;j<i;j++){
        if(j%23==0){
            continue outer;
        }
    }
}
```

for循环可以添加label用于多层嵌套循环跳出里层循环。

#### while循环

while语句也是一种循环结构，是一种“不定范围”循环,和for循环各有不同的用武之地。while语句事先不指定循环开始、结束的范围，只要测试条件满足，就一直执行循环体

* while循环没有显式定义循环变量，必须在while循环外先定义好循环变量，有时甚至可以没有循环变量。
* 循环体内的语句，必须使循环测试条件趋向不成立，否则会死循环

#### do while循环

do while循环是一种“后测试循环语句”。它不同于for循环和while循环每次都是“先测试条件是否满足，然后执行循环体”，do-while循环是“先执行循环体，然后测试条件是否满足”。

#### 关键字

* break

  表示立即终止循环，在for循环和while循环中都可以使用

  break用在while语句中，通常和while(true){}搭配使用

* continue

  用于跳过循环中的一个迭代，并继续执行循环中的下一个迭代。for循环更经常使用continue