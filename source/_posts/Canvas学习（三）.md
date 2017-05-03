---
title: Canvas学习（三）
date: 2017-03-09 08:57:54
tags: [Canvas,html5]
categories: 前端
---

接下来是Canvas学习的最后部分——动画。

## 动画循环

动画循环是创建动画效果的基础，动画循环的三要素：更新需要绘制的对象（如移动对象的位置）、清除画布、在画布上重新绘制对象。如下图所示：

![](/images/2017-03-09/画布中的典型循环.PNG)

### 创建循环

下面是一段简单的循环代码：

    //HTML
    <canvas id="myCanvas" width="500" height="500" style="border:1px solid #000000; display: block; ">

    </canvas>
    <button id="start">Start</button>
    <button id="stop">Stop</button>

    //js
    var canvas = $("#myCanvas");
    var cxt = canvas.get(0).getContext("2d");
    var canvasWidth = canvas.width();
    var canvasHeight = canvas.height();

    var startButton = $("#start");
    var stopButton = $("#stop");
    var ifPlay = true;

    startButton.hide();
    startButton.click(function(){
        $(this).hide();
        stopButton.show();
        ifPlay = true;
        animate();
    });
    stopButton.click(function(){
        $(this).hide();
        startButton.show();
        ifPlay = false;
    });

    function animate(){

        if(ifPlay){
            setTimeout(animate,33);
        }
    }
    animate();

以上代码中最重要的是`animate`函数，该函数使用`setTimeout`方法设置了一个定时器，每隔33毫秒调用一次`animate`函数，这样就创建了一个无限循环，可用来实现动画效果。`Start`、`Stop`按钮用来控制动画的启动和停止。

使用33毫秒作为动画循环时间间隔的原因：动画在每秒钟需要的帧数通常介于25到30帧之间，1秒=1000毫秒，1000除以30得33毫秒。

现在我们已经建立了基本的动画循环，接下来就需要在动画循环里添加三要素，即更新、清除和绘制过程了。

### 更新、清除、绘制

下面是一个简单的动画，使一个正方形每帧右移1像素。

    var x = 0;
    function animate(){

       x++;                                              //更新
       cxt.clearRect(0,0,canvasWidth,canvasHeight);     //清除
       cxt.fillRect(x,250,20,20);                      //绘制

       if(ifPlay){
           setTimeout(animate,33);
       }
    }
    animate();

效果如下：

![](/images/2017-03-09/简单动画.PNG)

## 记忆要绘制的形状

动画循环三要素中，清除和绘制都很简单，难点在更新，即：如何准确记忆要绘制的对象的内容及位置。

如果动画中的形状很少可以使用上一小节中的方法——简单变量来记忆，
但通常的动画中形状非常多，此时这种方法就非常笨拙了——需要复制大量代码，修改起来也非常复杂。其实需要解决的问题有两个：第一，不管形状的数量有多少，首先考虑如何存储每个形状的位置值；第二，在不复制代码的情况下如何绘制每个形状。针对这两个问题，可以考虑用对象和数组来实现。首先可以将每个形状视为一个对象，对象的属性即保存了形状的位置`(x,y)`、内容等信息；之后用数组来存储对象，就可通过循环来绘制每个形状。

示例代码：

    //用对象来定义形状
    var Shape = function (x,y,width,height) {
       this.x = x;
       this.y = y;
       this.width = width;
       this.height = height;
    };
    //用数组保存要绘制的所有形状
    var shapes = new Array();
    for(var i=0;i<10;i++){
       var x = Math.random()*250;
       var y = Math.random()*250;
       var width = height = Math.random()*50;
       shapes.push(new Shape(x,y,width,height));
    }

    function animate(){
       cxt.clearRect(0,0,canvasWidth,canvasHeight);  //清除
       //循环每个形状
       var shapesLength = shapes.length;
       for (var i = 0; i < shapesLength; i++) {
           var tmpShape = shapes[i];
           tmpShape.x++;   //更新
           cxt.fillRect(tmpShape.x, tmpShape.y, tmpShape.width, tmpShape.height);  //绘制
       };
       if(ifPlay){
           setTimeout(animate,33);
       }
    }
    animate();

效果：

![](/images/2017-03-09/保存形状.PNG)

## 圆周运动

有时候需要实现沿着圆周运动的动画效果，例如，沿着圆形轨道运行，如下图所示。

![](/images/2017-03-09/圆形轨道运行.PNG)

其实原理很简单：将一个形状放在圆周的边缘处（即周长上），以圆周的任意位置作为起点，在每次动画循环中，只需增加位于圆周上的形状的角度，就可以使形状沿着圆周运动。为简单起见，假设形状的起点位置总是周长上角度为0弧度的位置。我们可以使用三角函数来解决该问题，示意图如下：

![](/images/2017-03-09/三角函数.PNG)

假设圆点坐标为`(0,0)`，圆的半径为`r`，形状相对起点位置移动的角度为`a`。则，根据三角函数:`x=r*Math.cos(a)`，`y=r*Math.sin(a)`。这里的`a`是角度值，由于JavaScript中使用的单位是弧度，因此在实际计算时还需要进行转换。

综上，如果想要对一个形状实现圆周运动，就需要保存圆的半径`r`、形状当前运动位置的角度值。又圆周运动的圆的坐标不一定是`(0,0)`，因此还需要保存圆点坐标`(x,y)`。用对象描述如下：

    var Shape = function (x,y,width,height) {
       this.x = x;
       this.y = y;
       this.width = width;
       this.height = height;

       this.radius = Math.random()*30;
       this.angle = 0;
    };

其中，`(x,y)`表示的是形状做圆周运动时圆心的坐标，而不是形状的当前位置；`radius`表示半径；`angle`表示形状当前的角度值。

代码：

    //用对象来定义形状
    var Shape = function (x,y,width,height) {
       this.x = x;
       this.y = y;
       this.width = width;
       this.height = height;

       this.radius = Math.random()*30;
       this.angle = 0;
    };
    //用数组保存要绘制的所有形状
    var shapes = new Array();
    for(var i=0;i<10;i++){
       var x = Math.random()*250;
       var y = Math.random()*250;
       var width = height = Math.random()*50;
       shapes.push(new Shape(x,y,width,height));
    }

    function animate(){
       cxt.clearRect(0,0,canvasWidth,canvasHeight);  //清除
       //循环每个形状
       var shapesLength = shapes.length;
       for (var i = 0; i < shapesLength; i++) {
           var tmpShape = shapes[i];
           //更新
           var x = tmpShape.x+(tmpShape.radius*Math.cos(tmpShape.angle*(Math.PI/180)));
           var y = tmpShape.y+(tmpShape.radius*Math.sin(tmpShape.angle*(Math.PI/180)));
           tmpShape.angle += 5;
           cxt.fillRect(x, y, tmpShape.width, tmpShape.height);  //绘制
       };
       if(ifPlay){
           setTimeout(animate,33);
       }
    }
    animate();

效果：

![](/images/2017-03-09/圆周运动.PNG)

## 反弹

在之前的实现中，都没有考虑画布边界的问题，因此如果形状位置超出了画布边界，就会消失在视野中。我们理想的情景是：当形状位置达到边界时，会反弹回来，即向反方向运动，这样形状就会在画布中来回运动了。实现起来也很简单，只需在每次更新形状时检查形状是否超过了画布的边界，如果已经到达边界处，则反向改变形状运动的方向，这样它就会反弹回来。

代码：

    //反弹
    var Shape = function (x,y,width,height) {
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;

        this.reverseX = true;
        this.reverseY = false;
    };
    //用数组保存要绘制的所有形状
    var shapes = new Array();
    for(var i=0;i<10;i++){
        var x = Math.random()*250;
        var y = Math.random()*250;
        var width = height = Math.random()*50;
        shapes.push(new Shape(x,y,width,height));
    }

    function animate(){
        cxt.clearRect(0,0,canvasWidth,canvasHeight);
        //循环每个形状
        var shapesLength = shapes.length;
        for (var i = 0; i < shapesLength; i++) {
            var tmpShape = shapes[i];
            //更新
            //检查
            if(!tmpShape.reverseX){
                tmpShape.x += 2;
            }else{
                tmpShape.x -= 2;
            }
            if(!tmpShape.reverseY){
                tmpShape.y += 2;
            }else{
                tmpShape.y -= 2;
            }
            cxt.fillRect(tmpShape.x, tmpShape.y, tmpShape.width, tmpShape.height);  //绘制
            //检查是否超出边界
            if(tmpShape.x<0){
                tmpShape.reverseX = false;
            }else if(tmpShape.x+tmpShape.width>canvasWidth){
                tmpShape.reverseX = true;
            }
            if(tmpShape.y<0){
                tmpShape.reverseY = false;
            }else if(tmpShape.y+tmpShape.height>canvasWidth){
                tmpShape.reverseY = true;
            }
        };
        if(ifPlay){
            setTimeout(animate,33);
        }
    }
    animate();

对象中增加了两个属性`reverseX`、`reverseY`分别用来表示是否需要反弹，`true`表示需要，则在更新形状时让其向反方向运动；`false`表示不需要，则在更新形状时正方向运动即可。在每次绘制完后，还需要根据当前形状的位置来检查是否到达画布的边界，并更新`reverseX`、`reverseY`的值。这样就实现了一个简单的反弹动画效果。

## 实现高级动画

我们可以通过物理知识把动画效果进一步提高，创建更加逼真和具有动态效果的动画。无论是使用速度、加速度这些简单概念，还是构建一个包含不同方向作用力的完善系统，通过学习物理学，我们才能真实而全面地展现动画效果。下面先介绍一些基本的物理概念。

* 力： 力是作用于物体的推力或拉力，它是改变物体运动状态的原因。力是矢量，单位是牛顿（N）。
* 矢量：矢量是具有大小和方向的量。
* 质量：质量是一种阻碍物体在力的作用下加速的物理量，也用来衡量惯性。当力作用于物体时，质量将直接影响加速度的大小。质量的单位是千克。
* 重力：物体的质量受另一个物体引力的作用而产生的力称为重力。重力是物体存在重量的原因。
* 摩擦力：摩擦力是一种阻止一个物体沿着另一个物体表面运动的力。
* 速度：速度是指物体运动的方向和速率。它是一个矢量。
* 速率：指物体运动速度的大小，表示物体在一段时间内通过的距离。标量。
* 加速度：加速度是指物体的速度随时间变化的比值，矢量。

当我们创建自然流畅的动画时，了解力对物体运动状态的影响非常有用，因此还需要了解一下牛顿运动定律。

* 牛顿第一定律：任何一个物体在不受外力或受平衡力的作用时，总是保持静止状态或匀速直线运动状态，直到有作用在它上面的外力迫使它改变这种状态为止。
* 牛顿第二定律：物体的加速度跟物体所受的合外力成正比，跟物体的质量成反比，加速度的方向跟合外力的方向相同。`F=ma`
* 牛顿第三定律：两个物体之间的作用力和反作用力，在同一直线上，大小相等，方向相反。

以上就是一些基本的物理知识，下面将利用这些物理知识实现一个简单的动画效果：一个简单的太空场景，在太空深处有一个动态的小行星群，这里每个小行星的颜色是随机的。点击`Start`按钮，行星开始运动，点击`Stop`按钮，行星将会静止。

demo地址：{% post_link 实例demo之Canvas小动画 %}

关键代码：

    //定义形状
    var Asteroid = function(x, y,radius,mass,vX,vY,aX,aY,red,green,blue){
        this.x = x;
        this.y = y;
        this.radius = radius;
        this.mass = mass;

        this.vX = vX;
        this.vY = vY;
        this.aX = aX;
        this.aY = aY;

        //每个小球都有自己的颜色
        this.red = red;
        this.green = green;
        this.blue = blue;
    };

    //保存所有形状
    var asteroids = new Array();
    for(var i = 0;i<20;i++){
        //圆点坐标
       var x = 20+(Math.random()*(canvasWidth-40));
       var y = 20+(Math.random()*(canvasHeight-40));
        var radius = 5+Math.random()*10; //半径
        var mass = radius/2;   //质量
       //速度
        var vX = Math.random()*4-2;
        var vY = Math.random()*4-2;
        var aX = 0;
        var aY = 0;
        //颜色
        var red = Math.floor(Math.random()*255);
        var green = Math.floor(Math.random()*255);
        var blue = Math.floor(Math.random()*255);
        asteroids.push(new Asteroid(x,y,radius,mass,vX,vY,aX,aY,red,green,blue));
    }

    function animate(){
        //清除
        cxt.clearRect(0,0,canvasWidth,canvasHeight);
        var red = Math.floor(Math.random()*255);
        //cxt.fillStyle = "rgb(255,255,255)";
        //更新
        var asteroidsLength = asteroids.length;
        for(var i = 0; i < asteroidsLength; i++){
            var tmpAsteroid = asteroids[i];
            cxt.fillStyle = "rgb("+tmpAsteroid.red+","+tmpAsteroid.green+","+tmpAsteroid.blue+")";
           //检测是否和其他形状碰撞
            for(var j = i+1; j < asteroidsLength; j++){
                var tmpAsteroidB = asteroids[j];
                var dX = tmpAsteroidB.x - tmpAsteroid.x;
                var dY = tmpAsteroidB.y - tmpAsteroid.y;
                var distance = Math.sqrt((dX*dX)+(dY*dY));
                if(distance < tmpAsteroid.radius + tmpAsteroidB.radius){
                    //将小球的碰撞转换为理想碰撞，并利用坐标旋转公式计算转换后的坐标和速度
                    var angle = Math.atan2(dY, dX);    //旋转角度
                    var sine = Math.sin(angle);
                    var cosine = Math.cos(angle);
                    var x = 0;
                    var y = 0;
                    var vX = tmpAsteroid.vX * cosine + tmpAsteroid.vY * sine;
                    var vY = tmpAsteroid.vY * cosine - tmpAsteroid.vX * sine;

                    var xB = dX * cosine + dY * sine;
                    var yB = dY * cosine - dX * sine;
                    var vXb = tmpAsteroidB.vX * cosine + tmpAsteroidB.vY * sine;
                    var vYb = tmpAsteroidB.vY * cosine - tmpAsteroidB.vX * sine;

                    //理想碰撞后改变速度和坐标
                    //vX *= -1;
                    //vXb *= -1;
                    //能量守恒和动量守恒定律求解碰撞后的速度
                    var vTotal = vX - vXb;
                    vX = ((tmpAsteroid.mass - tmpAsteroidB.mass) * vX + 2 * tmpAsteroidB.mass * vXb) / (tmpAsteroid.mass + tmpAsteroidB.mass);
                    vXb = vTotal + vX;
                    xB = x + (tmpAsteroid.radius + tmpAsteroidB.radius);

                    //在旋转回之前的位置
                    tmpAsteroid.x = tmpAsteroid.x + (x * cosine - y * sine);
                    tmpAsteroid.y = tmpAsteroid.y + (y * cosine + x * sine);
                    tmpAsteroid.vX = vX * cosine - vY * sine;
                    tmpAsteroid.vY = vY * cosine + vX * sine;

                    tmpAsteroidB.x = tmpAsteroid.x + (xB * cosine - yB * sine);
                    tmpAsteroidB.y = tmpAsteroid.y + (yB * cosine + xB * sine);
                    tmpAsteroidB.vX = vXb * cosine - vYb * sine;
                    tmpAsteroidB.vY = vYb * cosine + vXb * sine;
                }
            }
            //更新位置
            tmpAsteroid.x += tmpAsteroid.vX;
            tmpAsteroid.y += tmpAsteroid.vY;
            //根据加速度更新速度
            if (Math.abs(tmpAsteroid.vX) < 10) {
              tmpAsteroid.vX += tmpAsteroid.aX;
            }
            if (Math.abs(tmpAsteroid.vY) < 10) {
              tmpAsteroid.vY += tmpAsteroid.aY;
            }

            //检查是否到边界
           if (tmpAsteroid.x-tmpAsteroid.radius < 0) {
              tmpAsteroid.x = tmpAsteroid.radius; // Move away from the edge
              tmpAsteroid.vX *= -1;
              tmpAsteroid.aX *= -1;
          } else if (tmpAsteroid.x+tmpAsteroid.radius > canvasWidth) {
            tmpAsteroid.x = canvasWidth-tmpAsteroid.radius; // Move away from the edge
            tmpAsteroid.vX *= -1;
            tmpAsteroid.aX *= -1;
          };

          if (tmpAsteroid.y-tmpAsteroid.radius < 0) {
            tmpAsteroid.y = tmpAsteroid.radius; // Move away from the edge
            tmpAsteroid.vY *= -1;
            tmpAsteroid.aY *= -1;
          } else if (tmpAsteroid.y+tmpAsteroid.radius > canvasHeight) {
            tmpAsteroid.y = canvasHeight-tmpAsteroid.radius; // Move away from the edge
            tmpAsteroid.vY *= -1;
            tmpAsteroid.aY *= -1;
          };
            //绘制
            cxt.beginPath();
            cxt.arc(tmpAsteroid.x, tmpAsteroid.y, tmpAsteroid.radius, 0, Math.PI*2);
            cxt.closePath();
            cxt.fill();
        }

        if(ifPlay){
            setTimeout(animate,33);
        }
    }
    animate();

`Asteroid`对象有11个属性：`(x,y)`是小行星的位置，`radius`是半径，`mass`是质量，`(vX,vY)`是速度，`(aX,aY)`是加速度，`(red,green,blue)`是颜色。在创建一个小行星时，这些参数的值是随机的，因此每个小行星都在不同的位置，有不同的大小、颜色、质量和速度。

其实该动画效果的难点在于小行星之间的碰撞检测。碰撞检测的两个关键步骤：第一，计算两颗小行星是否发生重叠；第二，在发生重叠时（即碰撞）以什么方式相互分离才显得更加逼真。检测重叠的方法很简单：检查两个圆心之间的距离是否小于这两个圆的半径之和。因此，难点在于怎样分离，即怎样弹开物体。

理想碰撞：两个圆发生碰撞时正好完全位于一条直线上，并且它们的中心点之间没有任何角度，此时只要反向改变速度的方向即可。如下图所示：

![](/images/2017-03-10/正面碰撞.PNG)

但通常情况下，两个圆都会以一定角度发生碰撞，此时仅仅反向改变速度是行不通的，两个圆应该以正确的角度互相弹开。如下图所示：

![](/images/2017-03-10/实际碰撞.PNG)

此时需要计算出两个圆之间的角度，并依次把每个圆的速度进行旋转，这样它们发生的碰撞就类似与上面提到的理想碰撞了，速度的状态转变如下图所示，这样圆的碰撞问题就转化成了一种简单的反向改变速度的问题。

![](/images/2017-03-10/两圆角度.PNG)
![](/images/2017-03-10/旋转两个圆.PNG)

对应的代码如下：

    var angle = Math.atan2(dY, dX);    //旋转角度
    var sine = Math.sin(angle);
    var cosine = Math.cos(angle);
    var x = 0;
    var y = 0;
    var vX = tmpAsteroid.vX * cosine + tmpAsteroid.vY * sine;
    var vY = tmpAsteroid.vY * cosine - tmpAsteroid.vX * sine;

    var xB = dX * cosine + dY * sine;
    var yB = dY * cosine - dX * sine;
    var vXb = tmpAsteroidB.vX * cosine + tmpAsteroidB.vY * sine;
    var vYb = tmpAsteroidB.vY * cosine - tmpAsteroidB.vX * sine;

这里用到了{% post_link 坐标旋转公式 %}

假设两个小行星发生的是完全弹性碰撞，则根据动量守恒和能量守恒公式可推导出碰撞后第一颗小行星的新速度为:

    vX = ((tmpAsteroid.mass - tmpAsteroidB.mass) * vX + 2 * tmpAsteroidB.mass * vXb) / (tmpAsteroid.mass + tmpAsteroidB.mass);

推到过程如下图所示：

![](/images/2017-03-10/公式推导.PNG)

这样，碰撞后的速度和位置改变为：

    //能量守恒和动量守恒定律求解碰撞后的速度
    var vTotal = vX - vXb;
    vX = ((tmpAsteroid.mass - tmpAsteroidB.mass) * vX + 2 * tmpAsteroidB.mass * vXb) / (tmpAsteroid.mass + tmpAsteroidB.mass);
    vXb = vTotal + vX;
    xB = x + (tmpAsteroid.radius + tmpAsteroidB.radius);

最后需要把这些小行星旋转到它们原来所在的位置，并使用新的速度。代码与上面旋转小行星的代码基本相反：

    //在旋转回之前的位置
    tmpAsteroid.x = tmpAsteroid.x + (x * cosine - y * sine);
    tmpAsteroid.y = tmpAsteroid.y + (y * cosine + x * sine);
    tmpAsteroid.vX = vX * cosine - vY * sine;
    tmpAsteroid.vY = vY * cosine + vX * sine;

    tmpAsteroidB.x = tmpAsteroid.x + (xB * cosine - yB * sine);
    tmpAsteroidB.y = tmpAsteroid.y + (yB * cosine + xB * sine);
    tmpAsteroidB.vX = vXb * cosine - vYb * sine;
    tmpAsteroidB.vY = vYb * cosine + vXb * sine;

通过以上过程，解决了碰撞检测问题。这样，这个小动画就比较完美地实现了。
