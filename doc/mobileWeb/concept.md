# 基础概念

## 像素

像素是图像最小单位的方格。

* 物理像素

  物理像素(physical pixel)，又称为设备像素(dp)，是设备屏幕上像素点的个数。

* CSS像素

  CSS像素，逻辑像素(logical pixel)，设备独立像素(dip)描述的是同一个东西。

* 设备像素比(dpr)

  dpr=dp/dip（同一方向轴上）。dpr=2表示1个CSS像素可以用2*2个物理像素绘制。

  如果一个设备屏幕的dpr<=1，那么称该屏幕为标清屏；否则，为高清屏。

* 缩放-放大

  缩放和放大的本质的是CSS像素的变化，缩放时dpr减小，放大时dpr增大。

* PPI

  PPI，pixels per inch，表示每英寸的物理像素点，又可以称为dpi，dots per inch。








