# Swiper触摸滑动插件

如果页面某些部分需要添加滑动效果，可以使用swiper实现。[Swiper官网](https://www.swiper.com.cn/)提供了swiper使用的方法。

## Swiper基础

### Swiper使用方法

1. 加载swiper提供的文件，swiper-bundle.min.js和swiper-bundle.min.css 文件
2. 完成swiper要求的HTML结构及响应的CSS样式
3. 初始化swiper

### Swiper常用API

* Swiper初始化

  Swiper初始化需要传两个参数，选择器和配置参数对象。

  | 参数           | 作用                               |
  | -------------- | ---------------------------------- |
  | initialSlide   | 初始显示的slide索引                |
  | direction      | swiper的方向，horizontal和vertical |
  | speed          | 切换速度                           |
  | effect         | 切换方式，slide、fade、cube...     |
  | grabCursor     | bool值，鼠标形状是否为小手         |
  | slidersPerView | 数值，swiper显示slide的个数        |
  | freeMode       | bool值，滑动位置是否不受限         |
  | loop           | bool值，切换循环                   |
  | on             | 对象，注册事件                     |

* 事件

  | 事件                       | 作用           |
  | -------------------------- | -------------- |
  | init                       | 初始化后执行   |
  | slideChangeTransitionStart | 切换开始时触发 |
  | slideChangeTransitionEnd   | 切换结束后触发 |

* 属性

  | 属性          | 意义              |
  | ------------- | ----------------- |
  | activeIndex   | 当前slide的索引   |
  | previousIndex | 前一个slide的索引 |
  | width         | swiper的宽        |

* 方法

  | 方法        | 作用                  |
  | ----------- | --------------------- |
  | slideNext() | 切换到下一张          |
  | slidePrev() | 切换到上一张          |
  | slideTo()   | 切换到指定索引的slide |

* 控制

  控制的属性书写在on值

  | 属性       | 作用                     |
  | ---------- | ------------------------ |
  | autoPlay   | bool值，自动切换         |
  | pagination | 对象，分页器设置         |
  | navigation | 对象，切换按钮设置       |
  | scrollbar  | 对象，滚动条设置         |
  | keyboard   | bool值，键盘控制切换     |
  | mousewheel | bool值，鼠标滚轮控制切换 |

  ```js
  {
      autoplay:true,//等同于以下设置
    /*autoplay: {
      delay: 3000,
      stopOnLastSlide: false,
      disableOnInteraction: true,
      },*/
  }
  ```

  