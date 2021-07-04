# 浮动

浮动最本质功能：用来实现并排。浮动使用float标签实现，属性值可以设置left、right。

浮动设计初衷是为了实现文字环绕效果。

浮动使用要点:

1. 要浮动，并排的盒子都要设置浮动
2. 父盒子需要有足够的空间

### 浮动的特性

* 浮动顺序贴靠特性

  子盒子会按顺序进行贴靠，如果没有足够空间，则会寻找再前一个兄弟元素

* 让元素block块状化(BFC)

  浮动的元素不再区分块级元素、行内元素，已经脱离了标准文档流，一律能够设置宽度和高度，即使它是span或者a标签等。
  
* 破坏性

  如果父容器没有设置高度(内部元素需要动态显示)，而内部元素设置浮动，那么父容器将被破坏，高度塌陷变为0。浮动的破坏性是为了实现文字环绕效果。

  破坏性会造成元素紧密排列特性(去空格化)
  
  > 其他破坏性的属性
  >
  > display：none；position：absolute/fixed/sticky

### 清除浮动

清除浮动，即清除浮动破坏性带来的影响。

清除浮动方法

1. 父盒子形成BFC

    让内部有浮动的父盒子形成BFC，它就能关闭住内部的浮动。此时最好的方法就是overflow:hidden属性

    不足：没有一个BFC声明能够在所有场合下都适用

2. 插入clear：both；

    * 给后面的父盒子设置clear:both属性。clear表示清除浮动对自己的影响，both表示左右浮动都清除。该方法的副作用是后面父盒子的margin-top会失效，因为前一个父盒子高度依旧为0。

    * 使用::after伪元素给盒子添加最后一个子元素，并且给::after设置clear:both。

        ```css
        .clearfix::after{
            content:'';
            clear:both;
            display:block;
        }
        ```

        不足：无法兼容IE6、IE7
    
    * 在两个父盒子之间“隔墙”，隔一个携带clear:both的盒子
    
        不足：裸露的div标签没有语义
    
3.  推荐使用

    ```css
    .clearfix:after{
        content:"";
        display:table;
        clear:both;
    }
    .clearfix{*zoom:1;}
    ```

### 使用浮动实现网页布局

注意事项：

1. 垂直显示的盒子，不要设置浮动，只有并排显示的盒子才要设置浮动!
2. 大盒子中的小盒子可以继续使用浮动
3. 不要节约盒子

浮动布局的问题：

1. 容错性低
2. 需要元素固定尺寸，很难重用
3. 低版本IE会出现问题

### 流体布局

浮动初衷是为了文字环绕效果，可以利用它实现流体布局。

1. 文字环绕变体

   中间标题使用text-align:center;两侧信息可以使用左浮动和右浮动

2. 单侧固定

   前一个元素使用width+float，后跟随信息的元素使用padding-left/margin-left

3. 智能自适应尺寸

   前一个元素使用float，后跟随元素使用display:table-cell;(IE8+)、display:inline-block(IE7)

### BFC规范和浏览器差异

BFC (Box Formatting Context，块级格式化上下文)是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然。

BFC作用

* BFC可以取消盒子margin塌陷
* BFC可以可以阻止元素被浮动元素覆盖

包裹(BFC)有以下三种表现

1. 收缩：水平方向尺寸变窄
2. 坚挺：高度存在
3. 隔绝：不会对外面产生影响

创建BFC方法

1. float值不为none

2. position值不是static或者relative

3. display值是inline-block、flex或者inline-flex

4. overflow：hidden；

   > overflow:hidden;表示溢出隐藏，溢出盒子边框的内容将会被隐藏
   > overflow:hidden;是非常好用的让盒子形成BFC的方法

BFC属性的差异

| 属性                 | 特点                                    |
| -------------------- | --------------------------------------- |
| overflow:hidden      | 自适应，但“溢出不可见”限制应用场景      |
| float                | 包裹性+破坏性，无法自适应，块状浮动布局 |
| position:absolute    | 脱离文档流                              |
| display:inline-block | 包裹性，无法自适应                      |
| display:table-cell   | 包裹性，天生无溢出，绝对宽度也能适应    |

**浏览器差异**

IE6、7浏览器使用haslayout机制，和BFC规范略有差异，比如IE浏览器可以使用zoom:1属性“让盒子拥有layout"
如果要制作兼容到lE6、7的网页时，尽量让网页布局变得简单，内部有浮动的盒子要设置height属性，规范编程，不要“玩杂技”