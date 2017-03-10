---
title: Canvas学习（二）
date: 2017-03-08 19:57:22
tags: [Canvas,html5]
categories: 前端
---

## 处理图像

### 加载图像

`drawImage(image,x,y)`。参数`image`可以是HTML`img`元素、HTML5`canvas`元素或HTML5`video`元素。例：

    var img = new Image();
    img.src = "wen.jpg";
    img.onload = function(){
      cxt.drawImage(img,0,0);
    };

在使用以上方法图像到画布时，会将完整尺寸的图像绘制到画布上，但超过画布边界的部分会被裁减掉。

### 调整和裁剪图像

`drawImage`方法还有两种调用方式：第一种调用可以调整图像大小，第二种调用可以同时调整和裁剪图像。`drawImage`的所有调用方式的唯一区别是所使用参数的个数和类型不同。

#### 调整图像

`drawImage(image,x,y,width,height)`,其中`width`和`height`表示希望绘制的图像宽度和高度。我们可以根据画布的宽高来等比例计算图像的宽高。

#### 裁剪图像

`drawImage(image,sx,sy,sw,sh,dx,dy,dw,dh)`，共9个参数：源图像、源图像的裁剪区原点坐标`(x,y)`、源图像的裁剪区宽和高、在画布上绘制图像的原点坐标`(x,y)`、在画布上绘制图像的宽和高。示意图如下：

![](/images/2017-03-08/裁剪图像.PNG)

### 阴影

代码：

    cxt.shadowBlur = 30;
    cxt.shadowColor = "rgb(0,0,0)";
    var img = new Image();
    img.src = "wen.jpg";
    img.onload = function(){
         cxt.drawImage(img,100,100,375,500);
    };

效果：

![](/images/2017-03-08/图像阴影.PNG)

### 图像变形

在画布中绘制图像后，就可以对它执行所有的2D渲染上下文方法。因此可以使用`translate`、`rotate`、`scale`等方法对图像进行旋转、平移、缩放与翻转等。注，如果`scale`方法使用负数缩放因子，就会使图像反转。

实例：

    cxt.shadowBlur = 30;
    cxt.shadowColor = "rgb(0,0,0)";
    cxt.rotate(Math.PI/4);
    var img = new Image();
    img.src = "wen.jpg";
    img.onload = function(){
        cxt.drawImage(img,100,100,375,500);
    };

效果：

![](/images/2017-03-08/img-rotate.PNG)

### 访问像素值

`getImageData(x,y,width,height)`方法，该方法有4个参数：要访问的像素区域原点坐标`(x,y)`、像素区域的宽度和高度。调用该方法会返回一个2D渲染上下文`ImageData`对象，该对象包含3个属性：`width`表示所访问像素区域的宽度、`height`表示像素区域的高度、`data`是一个包含所访问区域中全部像素信息的`CanvasPixelArray`。

`data`属性存储的是一个`CanvasPixelArray`，它是一个JavaScript一维数组。每一个像素用4个整数值表示，范围从0至255，分别表示红(r)、绿(g)、蓝(b)和阿尔法值(a)。因此，，数组的前4项（0~3）是第一个像素的颜色值，接下来4项(4~7)是第二个像素的颜色值，以此类推。因此，该数组的长度是所访问区域的像素个数乘以4。`CanvasPixelArray`中的像素排列顺序：左上角像素位于数组开头，右下角像素位于数组末尾。即在所访问的区域中，每一行像素时从左到右访问的，直至行尾，然后再同样从左到右访问下一行。

访问`(x,y)`坐标位置为`(2,2)`的中心像素：

    var imgData = cxt.getImageData(0, 0, 3, 3); // 3x3 grid
    var width = imgData.width;
    var pixelRed = (y-1)*(width*4)+((x-1)*4);
    var pixelGreen = pixelRed+1;
    var pixelBlue = pixelRed+2;
    var pixelAlpha= pixelRed+3;

使用如上公式可准确地计算出你需要从`CanvasPixelArray`中访问的像素。

### 绘制图像

代码：

    var imageData = cxt.createImageData(width,height)(创建一个200,200);
    var pixels = imageData.data;
    var numPixels = imageData.width * imageData.height;
    for(var i = 0; i< numPixels; i++){
        pixels[i*4] = 0;   //红
        pixels[i*4+1] = 0; //绿
        pixels[i*4+2] = 255; //蓝
        pixels[i*4+3] = 255;//透明度
    }
    cxt.putImageData(imageData,0,0);

效果：

![](/images/2017-03-08/创建和绘制像素.PNG)

其中，`createImageData(width,height)`方法返回一个包含所有常规属性的`ImageData`对象；`putImageData`方法可将`ImageData`对象画到画布上，该方法可接受3个或7个参数：`ImageData`对象、绘制像素数据的原点坐标`(x,y)`、脏矩阵的原点坐标`(x,y)`、脏矩阵的宽和高。

#### 随机绘制像素

代码片段：

    pixels[i*4] = Math.floor(Math.random()*255);   //红
    pixels[i*4+1] = Math.floor(Math.random()*255); ; //绿
    pixels[i*4+2] = Math.floor(Math.random()*255); ; //蓝
    pixels[i*4+3] = 255;//透明度

效果：

![](/images/2017-03-08/随机像素.PNG)

#### 创建马赛克效果

原理：将像素区域分割到一个栅格中，并为栅格中的每个块儿（马赛克块）随机设置一种颜色。

代码：

    //马赛克
    var imageData = cxt.createImageData(canvas.width(),canvas.height());
    var pixels = imageData.data;
    //马赛克块的个数
    var numTileRows = 20;
    var numTileCols = 20;
    //每个块的尺寸
    var tileWidth = imageData.width/numTileCols;
    var tileHeight = imageData.height/numTileRows;
    for(var r = 0; r< numTileRows; r++){
        for(var c = 0;c<numTileCols;c++){
            var red = Math.floor(Math.random()*255);   //红
            var green = Math.floor(Math.random()*255); //绿
            var blue = Math.floor(Math.random()*255);  //蓝
            for(var tr = 0;tr<tileWidth;tr++){
                for(var tc = 0;tc<tileHeight;tc++){
                    var trueX = (c*tileWidth)+tc;
                    var trueY = (r*tileHeight)+tr;
                    var pos = (trueY*(imageData.width*4))+(trueX*4);
                    pixels[pos] = red; //绿
                    pixels[pos+1] = green; //绿
                    pixels[pos+2] = blue; //蓝
                    pixels[pos+3] = 255;//透明度
                }
            }
       }
    }

效果：

![](/images/2017-03-08/马赛克.PNG)

### 基本图像效果

#### 反转图像

即用255减去像素现在的颜色值。

代码：

    //反转颜色
    var img = new Image();
    img.src = "wen.jpg";
    img.onload = function(){
       cxt.drawImage(img,0,0,1080,1440,0,0,canvas.width(),canvas.height());
       var imgData = cxt.getImageData(0,0,canvas.width(),canvas.height());
       var pixels = imgData.data;
       var numPixels = imgData.width * imgData.height;
       cxt.clearRect(0,0,canvas.width(),canvas.height());
       for(var i = 0; i< numPixels; i++){
           pixels[i*4] = 255 - pixels[i*4];   //红
           pixels[i*4+1] = 255 - pixels[i*4+1]; //绿
           pixels[i*4+2] = 255 - pixels[i*4+2]; //蓝
       }
       cxt.putImageData(imgData,0,0);
    };

效果：

![](/images/2017-03-08/反转颜色.PNG)

#### 灰度

方法：计算每个像素现有颜色值的平均值，并将该平均颜色作为相应像素三种颜色（红、绿和蓝）的值。

代码：

    for(var i = 0; i< numPixels; i++){
        var avg = (pixels[i*4]+pixels[i*4+1]+pixels[i*4+2])/3;
        pixels[i*4] = avg;   //红
        pixels[i*4+1] = avg; //绿
        pixels[i*4+2] = avg; //蓝
    }

效果：

![](/images/2017-03-08/灰度.PNG)

#### 像素化

和上面马赛克的励志类似，就是将图像按栅格分割，或者对每块儿的颜色取平均值，或者选取每块儿的颜色。

例：

    //像素
    var img = new Image();
    img.src = "wen.jpg";
    img.onload = function(){
        cxt.drawImage(img,0,0,1080,1440,0,0,500,500);
        var imgData = cxt.getImageData(0,0,canvas.width(),canvas.height());
        var pixels = imgData.data;
        cxt.clearRect(0,0,canvas.width(),canvas.height());
        //马赛克块数
        var numTileRows = 50;
        var numTileCols = 50;
        //每个块的尺寸
        var tileWidth = imgData.width/numTileCols;
        var tileHeight = imgData.height/numTileRows;
        for(var r = 0; r< numTileRows; r++){
            for(var c = 0;c<numTileCols;c++){
                //每块中心点
                var x = (c*tileWidth)+(tileWidth/2);
                var y = (r*tileHeight)+(tileHeight/2);
                var pos = (Math.floor(y)*(imgData.width*4))+(Math.floor(x)*4);
                var red = pixels[pos];   //红
                var green = pixels[pos+1]; //绿
                var blue = pixels[pos+2];  //蓝
                cxt.fillStyle = "rgb("+red+", "+green+", "+blue+")";
                cxt.fillRect(x-(tileWidth/2),y-(tileHeight/2),tileWidth,tileHeight);
            }

        }
        };

效果：

![](/images/2017-03-08/像素化.PNG)

圆形马赛克：

    cxt.beginPath();
    cxt.arc(x,y,tileWidth/2,0,Math.PI*2,false);
    cxt.closePath();
    cxt.fill();

效果：

![](/images/2017-03-08/像素化2.PNG)


## 视频处理

`drawImage`方法可以接受一个HTML5`video`元素作为输入，在画布中的视频处理与图像处理几乎完全一样。

实例：

    //html代码
    <canvas id="myCanvas" width="500" height="281">
        <video id="myVideo" src="Crooked.mp4" controls  width="500" height="281"></video>
    </canvas>
    <div>
        <button id="play">Play</button>
        <button id="stop">Stop</button>
    </div>

    //js代码：
    var canvas = $("#myCanvas");
    var cxt = canvas.get(0).getContext("2d");
    var video = $("#myVideo");
    // Trigger for video play button
    $("#play").click(function() {
     video.get(0).play();
    });

    // Trigger for video stop button
    $("#stop").click(function() {
     video.get(0).pause();
    });

    video.bind('play', function () {
     dravCanvas();
    });

    function  dravCanvas(){
      if(video.get(0).paused || video.get(0).ended) return;
      cxt.drawImage(video.get(0),0,0,500,281);
      var imgData = cxt.getImageData(0,0,canvas.width(),canvas.height());
        var pixels = imgData.data;
        cxt.clearRect(0,0,canvas.width(),canvas.height());
        //马赛克块数
        var numTileRows = 36;
        var numTileCols = 64;
        //每个块的尺寸
        var tileWidth = imgData.width/numTileCols;
        var tileHeight = imgData.height/numTileRows;
        for(var r = 0; r< numTileRows; r++){
            for(var c = 0;c<numTileCols;c++){
                //每块中心点
                var x = (c*tileWidth)+(tileWidth/2);
                var y = (r*tileHeight)+(tileHeight/2);
                var pos = (Math.floor(y)*(imgData.width*4))+(Math.floor(x)*4);
                var red = pixels[pos];   //红
                var green = pixels[pos+1]; //绿
                var blue = pixels[pos+2];  //蓝
                cxt.fillStyle = "rgb("+red+", "+green+", "+blue+")";
                cxt.fillRect(x-(tileWidth/2),y-(tileHeight/2),tileWidth,tileHeight);
            }
        }
     setTimeout(dravCanvas,30);
    }

效果截图：

![](/images/2017-03-08/像素化video.PNG)

其中，`play`和`pause`方法都属于`video`DOM对象，用于开始和停止视频。

我们通过`bind`方法创建了一个回调函数，通过监听`play`事件实现在视频开始播放时运行回调函数，回调函数将调用自定义函数`dravCanvas`，该函数包含所有创建像素化效果和绘制画布内容的功能。通过在`dravCanvas`函数末尾添加一个`setTimeout`调用，与视频的帧播放速度同步重复调用这个方法。
