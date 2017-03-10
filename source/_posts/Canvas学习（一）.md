---
title: Canvas学习（一）
date: 2017-03-08 11:42:13
tags: [Canvas,html5]
categories: 前端
---

## 基础知识
HTML5 `<canvas>`元素用于图形的绘制，通过脚本 (通常是JavaScript)来完成。`<canvas>`标签只是图形容器，必须使用脚本来绘制图形。

### 创建一个画布（canvas）

一个画布在网页中是一个矩形框，通过 `<canvas>` 元素来绘制。

    <canvas id="myCanvas" width="500" height="500"
    style="border:1px solid #000000;">
    </canvas>

  如果不设置canvas元素的尺寸，那么2D渲染上下文会被设置为使用默认宽度和高度，分别是300和150像素。

### 使用JavaScript来绘制图像

canvas 元素本身是没有绘图能力的。所有的绘制工作必须在 JavaScript 内部完成：

    <script>
        var canvas = document.getElementById('myCanvas');
        if(canvas && canvas.getContext){
            var cxt = canvas.getContext('2d');
          //线性渐变
            var grd = cxt.createLinearGradient(0,0,200,0);
            grd.addColorStop(0,"blue");
            grd.addColorStop(1,"pink");
            //矩形
             cxt.fillStyle = grd;
            cxt.fillRect(0,0,150,75);

        }
    </script>

`getContext("2d")`返回的对象是浏览器内建的`CanvasRenderingContext2D`对象，拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法。绘图功能是通过该对象实现的。

因此，canvas元素并非Canvas中最强大的部分，真正的关键部分是2D渲染上下文，这是我们真正绘制图形的地方。canvas元素的用途只是作为2D渲染上下文的包装器，它包含绘图和图形操作所需要的全部方法和丰富功能。坐标系统的1个单位通常相当于屏幕的1个像素。

### Canvas坐标

canvas元素是一张画布，这张画布的原点在元素的左上角，水平的是x轴，竖直的是y轴。沿原点向右、向下是正值，向左、向上是负值。

### 绘制基本图形和线条

#### 线条


    var canvas = document.getElementById('myCanvas');
    var cxt = canvas.getContext('2d');
    cxt.beginPath(); //开始路径
    cxt.moveTo(40.40); //设置路径原点
    cxt.lineTo(340,40); //设置路径终点
    cxt.closePath(); //结束路径
    cxt.stroke(); //绘出路径轮廓

#### 矩形

    //fillRect(x, y, width, height)
    cxt.fillStyle = "#FF0000"; //设置颜色
    cxt.fillRect(40, 40, 100, 100); // 绘制一个矩形并给它填充颜色
    cxt.strokeRect(200, 300, 200, 100); // 绘制一个矩形并给它绘制边框

`fillRect(x, y, width, height)`中，`x`、`y`用来指定矩形左上角顶点的坐标；`width`指定矩形的宽；`height`指定矩形的高。

#### 圆形

    context.beginPath(); // 开始路径
    context.arc(230, 90, 50, 0, Math.PI*2, false); // 绘制一个圆形
    context.closePath(); // 结束路径
    context.fill(); // 填充路径

`arc(x, y, r, startAngle, endAngle, anticlockwise)`中，`x`、`y`指定圆点坐标；`r`指定半径；`startAngle`、 `endAngle`指定开始角度和结束角度；`anticlockwise`指定弧线的绘制方向，该参数可选，但是如果不传入这个参数Firefox会抛出一个错误，因此最好保留这个参数。

在Canvas中，一条弧线是由一条曲线定义的，它从与原点`(x,y)`距离为一个半径且角度为开始角度的位置开始，这条路径最后停在离原点`(x,y)`一个半径且角度为结束角度的位置上。此外，Canvas中的角度是以弧度而不是角度为单位的。简单地说，360度（一个完整的圆）是2π（`PI`的2倍）弧度。

![](/images/2017-03-08/绘制圆弧.PNG)

### 样式

    cxt.fillStyle = "#FF0000"; //指定填充颜色
    cxt.strokeStyle = "rgb(255, 0, 0)"; //指定描边颜色
    cxt.lineWidth = 5; // 指定线宽

### 文字

    var text = "Hello, World!"; // 要绘制的文字
    cxt.font = "italic 30px serif";// 修改字号和字体 并设置为斜体
    cxt.fillText(text, 40, 40); // 绘制文字
    cxt.strokeText(text, 80, 80); // 绘制描边文字

`strokeText(text, x, y)`中，`text`是准备绘制的文字，`(x,y)`是文本原点（左下角）的坐标值。

由于Canvas中的文本是以图形形式绘制的，因此它无法像HTML文档中的普通文字那样用鼠标指针选取——它实际上不是文本，只是像文本而已。

### 擦除Canvas

#### clearReact方法

`clearReact(x,y,width,height)`中`x`、`y`用来指定矩形左上角顶点的坐标；`width`指定矩形的宽；`height`指定矩形的高。该矩形区域就是要擦除的区域。使用该方法只会清除指定区域，并不会完全重置Canvas上的所有内容（例如：样式、颜色等会保留）。

#### 宽度/高度技巧

每当重新设置一个canvas元素的width和height属性时，Canvas都会自动清除内容并返回其原始状态。

    var canvas = $("#myCanvas");
    var context = canvas.get(0).getContext("2d");
    context.fillStyle = "rgb(255, 0, 0)";
    context.fillRect(40, 40, 100, 100);

    context.beginPath();
    context.arc(230, 90, 50, 0, Math.PI*2, false);
    context.closePath();
    context.fill();

    canvas.attr("width", canvas.width());
    canvas.attr("height", canvas.height());

    context.fillRect(40, 40, 100, 100);

该技巧的缺点是，它会完全重置Canvas上的所有内容，包括样式和颜色。所以，只有准备完全重置Canvas时才能使用该方法。

### 使Canvas填满浏览器窗口

代码如下：

    //HTML
    <canvas id="myCanvas" width="500" height="500">
    	<!-- Insert fallback content here -->
    </canvas>

    //js
    $(document).ready(function() {
    	var canvas = $("#myCanvas");
    	var context = canvas.get(0).getContext("2d");

    	$(window).resize(resizeCanvas);

    	function resizeCanvas() {
    		canvas.attr("width", $(window).get(0).innerWidth);
    		canvas.attr("height", $(window).get(0).innerHeight);
    		context.fillRect(0, 0, canvas.width(), canvas.height());
    	};

    	resizeCanvas();
    });

    //css
    * { margin: 0; padding: 0; }
    html, body { height: 100%; width: 100%; }
    canvas { display: block; }

## Canvas高级功能

### 保存和恢复画布状态

在画布中，绘图状态指的是描述某一时刻2D渲染上下文外观的整套属性。用于描述画布绘图状态的全部属性为：变换矩阵、裁剪区域（cliping region）、globalAlpha、globalCompositeOperation、strokeStyle、fillStyle、lineWidth、lineCap、lineJoin、miterLimit、shadowOffsetX、shadowOffsetY、shadowBlur、shadowColor、font、textAlign、textBaseline。


注：画布上的当前路径和当前位图（正在显示的内容）并不属于状态。

保存绘图状态：`save`方法。

    var cxt = canvas.getContext('2d');
    cxt.fillRect(0,0,150,150);
    cxt.save();

恢复绘图状态：`restore`方法。

    var cxt = canvas.getContext('2d');
    cxt.fillRect(0,0,150,150);
    cxt.save();
    cxt.fillStyle="blue";
    cxt.fillRect(100,100,100,100);
    cxt.restore();
    cxt.fillRect(150,150,150,150);

2D渲染上下文会保存一个绘图状态栈，默认栈是空的。每调用一次`save`方法，就会有一个新状态添加到这个栈；每调用一次`restore`方法，就会取出最后添加到栈中的绘图状态，并将它应用于2D渲染上下文，用所保存的状态覆盖全部现有的样式。

### 变形

#### 平移

即将2D渲染上下文的原点从一个位置移动到另一个位置。使用`translate`方法。`(x,y)`参数是移动后2D渲染上下文的坐标原点。

    cxt.translate(150.150);

注：该方法实际移动的是2D渲染上下文的坐标原点，而不是所绘制的对象，也不是`canvas`元素。移动后，再绘制新的对象时都会相对于新的2D渲染上下文的坐标原点，但移动之前所绘制的对象保持不变。

#### 缩放

即调整2D渲染上下文的尺寸，使用`scale`方法。`(x,y)`参数是缩放倍数。

    cxt.scale(2,2);

#### 旋转

使用`rotate`方法，参数为以弧度为单位的2D渲染上下文旋转角度值。

cxt.rotate(Math.PI/4); // 旋转45度

`rotate`方法是把2D渲染上下文绕其原点进行旋转的。

#### 变换矩阵

变换矩阵是一组数字。它们各自描述一个特定变形类型。矩阵分成多个列和行，在画布中使用的是一个3*3矩阵，如下图所示。

![](/images/2017-03-08/2D渲染上下文的变化矩阵.PNG)

重要的是前两行，其中包含的数字值对应画布中使用的`a`至`f`。一个新的2D渲染上下文将包含一个全新的变换矩阵，即单位矩阵：

![](/images/2017-03-08/单位矩阵.PNG)

操作变换矩阵的方法是`transform`和`setTransform`。`transform`方法有6个参数，分别对应变换矩阵中的每一个值，第一个表示`a`,最后一个表示`f`；`setTransform`方法的作用是将矩阵重置为单位矩阵。

    cxt.setTransform(1, 0, 0, 1, 0, 0); // Identity matrix
    var xScale = Math.cos(0.7854);
    var ySkew = -Math.sin(0.7854);
    var xSkew = Math.sin(0.7854);
    var yScale = Math.cos(0.7854);
    var xTrans = 200;
    var yTrans = 200;
    cxt.transform(xScale, ySkew, xSkew, yScale, xTrans, yTrans);
    cxt.fillRect(-50, -50, 100, 100);

### 合成

在画布上绘制的所有东西是已经合成的，这意味着绘制的所有内容都会与已经绘制的现有元素合并在一起。这实际上都是基本合成，只是将一些内容叠加到另一些内容之上。下面是两个全局合成属性，因为是全局的，所以在修改这两个属性会影响到修改之后所绘制的全部内容。

#### globalAlpha

该属性的取值在`0.0`(全透明)与`1.0`(不透明)之间，默认值是1.0。即`globalAlpha`属性会影响到将要绘制的对象的透明度。

    cxt.fillStyle = "rgb(63, 169, 245)";
    cxt.fillRect(50, 50, 100, 100);
    cxt.globalAlpha = 0.5;
    cxt.fillStyle = "rgb(255, 123, 172)";
    cxt.fillRect(100, 100, 100, 100);

#### globalCompositeOperation

该属性定义了对2D渲染上下文上所有绘制图形执行的合成类型，所谓合成，即一个图形叠加到另一个图形之上。`globalCompositeOperation`的默认值是`source-over`(源覆盖于目标之上)，源是绘制的新图形，而目标则是可能已经绘制了图形的2D渲染上下文。

`globalCompositeOperation`支持11种选择：

* `source-over`：源覆盖于目标之上
* `destination-over`：目标覆盖于源之上
* `source-atop`：源覆盖于目标之上，重叠区域二者都不透明，重叠区域之外的目标不透明，源透明
* `destination-atop`：目标覆盖于源之上，重叠区域二者都不透明，重叠区域之外的源不透明，目标透明
* `source-in`：在源与目标重叠的区域只绘制源，而不重叠的部分都变成透明的
* `destination-in`：在源与目标重叠的区域只绘制目标，而不重叠的部分都变成透明的
* `source-out`：在源与目标不重叠的区域只绘制源，其他部分都变成透明的
* `destination-out`：在源与目标不重叠的区域只绘制目标，其他部分都变成透明的
* `lighter`：源与目标的重叠区域两者的颜色值相加
* `copy`：只绘制源，覆盖掉目标
* `xor`：只绘制不重叠的源与目标区域，重叠的部分变成透明

    cxt.fillStyle = "rgb(63,169,245)";
    cxt.fillRect(50,50,100,100);
    cxt.globalCompositeOperation = "xor";
    cxt.fillStyle = "rgb(255,123,172)";
    cxt.fillRect(100,100,100,100);

### 阴影

通过4个全局属性进行控制：`shadowBlur`、`shadowColor`、`shadowOffsetX`、`shadowOffsetY`

    cxt.shadowBlur = 0;
    cxt.shadowOffsetX = 10;
    cxt.shadowOffsetY = 10;
    cxt.shadowColor = "rgba(100,100,100,0.5)";
    cxt.fillRect(50,50,100,100);

### 渐变

`fillStyle`和`strokeStyle`都可以接受以`CanvasGradient`对象表示的渐变颜色的值。画布支持两种类型的渐变：线性渐变(`createLinearGradient`)和放射渐变（`createRadialGradient`）。这两种方法都返回一个`CanvasGradient`对象，可以使用该对象本身的`addColorStop`方法对它进行进一步处理。

线性渐变：

    var gradient = cxt.createLinearGradient(0, 0, 0, canvas.height);
    gradient.addColorStop(0, "rgb(0, 0, 0)");
    gradient.addColorStop(1, "rgb(255, 255, 255)");
    cxt.fillStyle = gradient;
    cxt.fillRect(0, 0, canvas.width, canvas.height);

`createRadialGradient`方法有4个参数：渐变起点的`(x,y)`坐标和渐变终点的`(x,y)`坐标。

放射渐变：

    var gradient = cxt.createRadialGradient(300,300,10,100,100,50);
    gradient.addColorStop(0,"rgb(0,0,0)");
    gradient.addColorStop(1,"rgb(150,150,150");
    cxt.fillStyle = gradient;
    cxt.fillRect(0,0,canvas.width,canvas.height);

`createRadialGradient`方法有6个参数：前3个参数描述一个圆（开始圆），后3个参数描述另一个圆（结束圆）。这两个圆本身不仅描述了方向及渐变的起止位置，而且还描述了渐变的形状。实际的渐变效果是连接两个圆的椎体，其中开始圆之前的椎体部分显示偏移值为0的颜色，而结束圆之后的椎体部分则显示偏移值为1的颜色，如下图所示：

![](/images/2017-03-08/放射渐变工作原理.PNG)

因此，如果将开始圆和结束圆放置在同一个位置，就可以得到我们想要的放射渐变效果。

### 复杂路径

    cxt.beginPath();
    cxt.moveTo(100, 50);
    cxt.lineTo(150, 150);
    cxt.lineTo(50, 150);
    cxt.closePath();
    cxt.stroke();
    cxt.fill();

以上代码可以绘制一个三角形，效果如下：

![](/images/2017-03-08/绘制三角形.PNG)

其中，`moveTo`方法会创建一条全新的子路径；`lineTo`方法只是沿着一条已有的子路径继续画线,每次调用都是从子路径的最后一个坐标开始画线；`closePath`方法会画一条线连接子路径的最后一个点和第一个点——封闭路径。

#### 贝塞尔曲线

两个方法：`quadraticCurveTo(cpx,cpy,x,y)`(二次贝塞尔曲线)和`bezierCurveTo(cpx1,cpy1,cpx2,cpy2,x,y)`(三次贝塞尔曲线)，构造如下：

![](/images/2017-03-08/贝塞尔曲线.PNG)

实例1：

    cxt.lineWidth = 5;
    cxt.beginPath();
    cxt.moveTo(50,250);
    cxt.quadraticCurveTo(250,100,450,250);
    cxt.stroke();

效果：

![](/images/2017-03-08/quadraticCurveTo.PNG)

实例2：

    cxt.lineWidth = 5;
    cxt.beginPath();
    cxt.moveTo(50,250);
    cxt.bezierCurveTo(150,50,350,450,450,250);
    cxt.stroke();
    cxt.bezierCurveTo(150,50,350,450,450,250);

效果：

![](/images/2017-03-08/bezierCurveTo.PNG)

### 将画布导出为图像

画布有一个`toDataURL`方法，可以将画布绘图转换为一个数据URL，我们可以通过它在浏览器上显示一个图像。

    cxt.save();
    cxt.fillRect(50,50,100,100);
    cxt.fillStyle = "rgb(255,0,0)";
    cxt.fillRect(100,100,100,100);
    cxt.restore();
    cxt.fillRect(150,150,100,100);
    var dataURL = canvas.toDataURL();
    console.log(dataURL);
