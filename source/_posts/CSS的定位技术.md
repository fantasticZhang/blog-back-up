---
title: CSS的定位技术
date: 2016-11-21 20:39:57
categories: 前端
tags: CSS
---

CSS使用top、left、right、bottom设置元素的二维（x轴和y轴）偏移量。使用z-index设置元素垂直于屏幕的方向，也就是“z轴”的偏移量。

CSS使用position选项来定义元素的定位属性，该选项有五个可选值：static、relative、absolute、fixed、inherit，默认值是static。inherit属性表示继承父元素的定位属性，因此只需掌握static、relative、absolute、fixed这四种定位属性的特性即可。

### 相对定位技术

相对定位即相对于文档流中的其他已定义的元素位置进行定位。relative和static都是相对于文档其他元素进行定位，都属于相对定位的范畴，区别在于一个可以控制位移，一个不能。

#### static（默认值）

如果使用默认值，在CSS中为元素定义top、left、right、 bottom、z-index都不会生效。也就是说，如果想设置元素的偏移量和z-index，必须为元素定义position属性（static除外）。

#### relative

relative的表现和默认值一样，只不过可以通过设置偏移量和z-index来控制相对于其正常位置进行的偏移。

### 绝对定位

绝对定位的元素有以下几个特点：

* 块级元素的宽度在未定义时不再为100%，而是根据内容自动调整。
* 在不定义z-index的情况下，absolute元素会覆盖在其他元素之上。
* 它会脱离正常的文档流，不再占据空间，类似于浮动后的效果。

absolute和fixed都属于绝对定位的范畴，都遵循以上3个特点。

#### absolute

absolute是相对上一个不为static的父元素进行绝对定位。也就是说，如果不指定父元素的position，absolute将相对于整个html文档进行绝对定位。

#### fixed

fixed是相对于浏览器窗口进行定位。也就是说，不论网页如何滚动，该元素始终停留在屏幕的某个位置上。例如：我们希望侧边控制栏始终对用户可见，就可以使用`position:fixed`来进行定位。
	