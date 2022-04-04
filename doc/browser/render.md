# 浏览器渲染

1. 处理 HTML 并构建 DOM 树。

2. 处理 CSS 构建 CSSOM 树。

3. 将 DOM 与 CSSOM 合并成一个渲染树。

   渲染树的节点被称为渲染对象，渲染对象是一个包含有颜色和大小等属性的矩形，渲染对象和 DOM 元素相对应，但这种对应关系不是一对一的，不可见的 DOM 元素不会被插入渲染树。

4. 根据渲染树来布局，计算每个节点的位置。

5. 调用 GPU 绘制，合成图层，显示在屏幕上。

渲染优化

1. JavaScript文件引入放在body最后，如果放在前面，使用defer属性
2. CSS文件使用link，减少选择器层级
3. 减少重绘、回流
   * 使用translate代替top
   * 使用visibility:hidden代替display:none
   * 需要插入多个节点，先插入到documentFragment，再添加到页面