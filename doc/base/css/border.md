# 边框与圆角

### 边框

边框由boder属性设置。

* 边框三要素

  border由线宽、线型和颜色来描述。设置边框的颜色为transparent可以使边框透明。

  ```css
  border:1px solid white;
  ```

  三种常见线型

    1. solid实线
    2. dashed虚线
    3. dotted点状线
  
  子属性
  
  * border-width
  * border-style
  * border-color

* 四个方向的边框

  | 属性          | 说明   |
  | ------------- | ------ |
  | border-top    | 上边框 |
  | border-right  | 右边框 |
  | border-bottom | 下边框 |
  | border-left   | 左边框 |

* 去掉边框

  边框属性设置none即可去掉边框。
  
* 制作三角形

  1. 设置height、width都为0
  2. 设置boder，其颜色为transparent
  3. 选择某一方向的边框，将其颜色设置非透明

> border只写线型属性能够显示边框，这是因为线宽和颜色有默认值。

### 圆角

使用border-radius属性可以设置边框圆角。属性值通常为px单位，表示圆角半径。如果属性值使用百分数描述，那么该百分数表明圆角起始于每条边的哪里。

* 单独设置四个圆角

  ```css
  border-radius:10px 20px 30px 40px;
  ```

  依次表示左上、右上、右下、左下。

  属性值可以同margin、padding一样书写三个、两个。

  > 两个值时，第一个值代表左上角和右下角的圆角，第二个值表示右上角和左下角的圆角。
  >
  > 三个值时，第一个值为左上角，第二个值为右上角和左下角，第三个值为右下角。

* 子属性

  | 属性                       | 说明   |
  | -------------------------- | ------ |
  | border-top-left-radius     | 左上角 |
  | border-top-right-radius    | 右上角 |
  | border-bottom-left-radius  | 左下角 |
  | border-bottom-right-radius | 右下角 |

### 盒子阴影

盒子阴影使用box-shadow属性来实现，一般用四个值描述：x偏移、y偏移、模糊量、颜色。

```css
box-shadow:10px 20px 10px red;
```

* 内阴影

  在box-shadow属性值前使用inset，可以将外阴影改为内阴影。

* 阴影延展

  如果box-shadow使用了五个值，则第四个值表示阴影向外延伸的距离。

* 多阴影

  box-shadow可以使用`，`隔开多个阴影属性值，从而使盒子叠加多种阴影。