# 选择器

## CSS2.1选择器

### 标签选择器

标签选择器也称元素选择器、类型选择器,它直接使用元素的标签名当做选择器，将选择页面上所有该种标签。

* 特点

  标签选择器将选择页面上所有该种标签，无论这个标签所处位置的深浅

* 常见用途

  通常用于标签的初始化

* 使用示例

  ```css
  ul {
      list-style:none; // 去掉无序列表的小圆点
  }
  
  a {
      text-decration:none; // 去掉超级链接的下划线
  }
  ```

### id选择器

id选择器可以使用#前缀，选择指定id的标签。

* id属性

  标签可以有id属性，该属性值是这个标签的唯一标识。

  同一个页面上不能有相同id的标签。

  id的名称只能由字母、数字、下划线、短横构成，且不能以数字开头，字母区分大小写，但习惯上一般为小写字母。

* 使用示例

  ```css
  # apple{
      color:red;
  }
  ```

### class选择器

使用`.className`选择指定class的标签。

* class类名

  class属性表示类名，类名的命名规范与id的命名规范相同。

  多个标签可以是相同类名。

  同一个标签可以同时属于多个类，类名用空格隔开。

* 原子类

  在做网页项目前，可以将所有的常用字号、文字颜色、行高、外边距、内边距等都设置为单独的类。HTML标签就可以“按需选择”它的类名了，这样可以非常快速的添加一些常见样式。

* 使用示例

  ```html
  <style>
      .fs12{
          font-size:12px;
      }
      .color-red{
          color:red;
      }
  </style>
  <p class="fs12 color-red">
      class选择器
  </p>
  ```

### 复合选择器

复合选择器其实是三种选择器。

1. 后代选择器

   CSS选择器中，使用空格表示后代。

   > 在某标签内的所有标签都是该标签的后代。

2. 交集选择器

   选择带有.spec类的标签需要使用交集选择器。

3. 并集选择器

   并集选择器也叫分组选择器，逗号表示分组。

4. 代码示例

   ```html
   <style>
       .box p{
           color:red;
       }
       div.box{
           background-color:green;
       }
       ul, ol {
           list-style:none;
       }
   </style>
   <div class="box">
       <p>
           后代选择器
       </p>
       <ol>
           <li><p>
               列表中的段落
               </p></li>
       </ol>
   </div>
   ```

### 伪类

伪类是添加到选择器的描述性词语，指定要选择的元素的特殊状态，超级链接拥有4个特殊状态：`:link`、`:visited`、`:hover`、`:active`。

链接伪类的顺序，a:hover必须置于a:link和a:visited之后，才有效，a:active必须在a：hover之后，才有效。而link、visited两个伪类之间顺序无所谓，谁在前都可以。

## CSS3新增选择器

### 元素关系选择器

| 名称           | 举例   | 意义                            |
| -------------- | ------ | ------------------------------- |
| 子选择器       | div>p  | div的子标签p                    |
| 相邻兄弟选择器 | img+p  | 图片后面紧跟着的段落            |
| 通用兄弟选择器 | p~span | p元素之后的所有同层级的span元素 |

* 子选择器

  只会匹配作为第一个元素直接后代元素。

* 相邻兄弟选择器

  当第二个元素紧跟在第一个元素之后，并且两个元素的父元素是同一个，则第二个元素将被选中。

* 通用兄弟选择器

  a~b选择a元素之后的所有同层级的b元素。

元素关系选择器兼容到IE7。

### 序号选择器

| 选择器               | 意义                                      |
| -------------------- | ----------------------------------------- |
| :first-child         | 选取属于其父元素的首个子元素              |
| :last-child          | 选取属于其父元素的最后一个子元素          |
| :nth-child(n)        | 匹配属于其父元素的第n个子元素             |
| :nth-of-type(n)      | 匹配属于其父元素的倒数第n个子元素         |
| :nth-last-child(n)   | 匹配属于其父元素的第n个某类型的子元素     |
| :nth-last-of-type(3) | 匹配属于其父元素的倒数第n个某类型的子元素 |

nth-child()可以写成an+b的形式。

如果.box中第n个元素不是p，则.box p:nth-child(n)选择器无效。带有child的选择器在计算元素序号时不论元素类型，但在显示时会检查类型是否匹配。

除了:first-child选择器是IE7兼容，其余序号选择器IE9兼容。

### 属性选择器

| 选择器                     | 意义                                                   |
| -------------------------- | ------------------------------------------------------ |
| Element[attribute=value]   | 用于选取属性值为指定词汇的元素                         |
| Element[attribute~=value]  | 用于选取属性值中包含指定词汇的元素，该词汇必须空格隔开 |
| Element[attribute^=value]  | 匹配指定属性的指定value值开头的元素                    |
| Element[attribute$=value]  | 匹配指定属性的指定value值结束的元素                    |
| Element[attribute*=value]  | 用于选取属性值中含有指定词汇的元素                     |
| Element[attribute\|=value] | 匹配属性值是以“value-“开头的元素                       |

属性选择器开发中很少使用，IE9开始兼容。

### CSS3新增伪类

| 选择器           | 意义                                                    |
| ---------------- | ------------------------------------------------------- |
| Element:focus    | 在表单元素获得焦点时选择该元素                          |
| Element:enabled  | 匹配每个已启用的元素（大多用在表单元素上）              |
| Element:disabled | 匹配每个被禁用的元素（大多用在表单元素上）              |
| Element:checked  | 匹配每个已被选中的 input 元素（只用于单选按钮和复选框） |
| Element:empty    | 匹配没有子元素（包括文本节点）的每个元素                |
| :root            | 选择根元素，html标签                                    |

### 伪元素

伪元素表示虚拟动态创建的元素。

| 伪元素         | 意义                                                         |
| -------------- | ------------------------------------------------------------ |
| ::before       | 创建一个伪元素，其将成为匹配选中的元素的第一个子元素，必须设置content属性表示其内容 |
| ::after        | 创建一个伪元素，其将成为匹配选中的元素的最后一个子元素，必须设置content属性表示其内容 |
| ::selection    | 文档中被用户高亮的部分(使用鼠标圈选的部分)                   |
| ::first-letter | 选中块级元素第一行的第一个字母                               |
| ::first-line   | 选中块级元素第一行全部文字                                   |

## 层叠性和选择器权重计算

CSS全名叫“层叠式样式表”，层叠式是它重要的性质。

> 层叠性：多个选择器可以同时作用于同一个标签，效果叠加。

### 冲突处理

CSS有严密的冲突处理规则来应对多个选择器定义的属性值有冲突的情况。如果多个选择器选择到同一个元素且设置的属性值存在冲突，可依据如下规则判断其属性值。

* 如果选择器的某属性添加了!important，则元素样式为该属性

  > 如果想要将某个选择器的某条属性提升权重，可以在属性后面写!important。添加该标识的属性权重最高。

* 否则查看是否有行内样式

  如果有则元素样式为行内样式

  * 否则计算选择器权重

    > 复杂选择器可以通过(id的个数，class的个数，标签的个数)的形式，计算权重。id权重>class权重>标签权重

    如果权重不同，则应用权重高的样式

    如果权重相同，则看在内部样式中，哪个选择器书写位置靠后。