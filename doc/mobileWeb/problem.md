# 移动端常见问题

## 浏览器兼容性

html、css、js都存在浏览器兼容性问题。对于某元素在各浏览器上的兼容情况，可以在[caniuse](https://caniuse.com/)上查询。

* html

  html5在部分低版本PC浏览器上不支持，如果想要兼容这些浏览器，可以引入html5shiv的js文件。移动端兼容性好。

* js

  如果js一些函数存在兼容性问题，那么要解决这个问题需要使用特性检测，而非浏览器检测。

  ```javascript
  let requestAnimationFrame = window.requestAnimationFrame||
      window.webkitRequestAnimationFrame||
      window.mozRequestAnimationFrame||
      window.oRequestAnimationFrame||
      window.msRequestAnimationFrame||
      function(fn){
          setTimeout(fn,16);
      };
  ```

* css

  css特性的兼容性问题存在两类情况：支持但需要加厂商前缀、完全不支持。对于第一种情况，可以使用emmet插件在书写时插入或者利用预处理语言。

  完全不支持，可以在modernizr上定制检测某特性兼容情况的js文件。

## 动画

网页上的动画效果，实现主体是dom或canvas，实现方式是js或css3。

js中能够实现动画效果的函数是requestAnimationFrame、setTimeout、setInterval，优先考虑使用requestAnimationFrame。css中能够实现动画效果的属性是transition、animation。

如果使用dom实现动画，首先考虑使用css3，再次考虑使用js。

## click延迟

移动端上为了判断click和doubleclick，click会出现300ms的延迟，如果在延迟期间又触发了一次click，则该事件为doubleclick。doubleclick用于缩放页面。

解决click延迟问题的方法有两种。

1. 在`name="viewport"`的meta标签的content中存在width=devic-width，那么浏览器就会默认不存在doubleclick，消除延迟。但是有些浏览器并没有遵守照这个规则。
2. 使用第三方库fastclick