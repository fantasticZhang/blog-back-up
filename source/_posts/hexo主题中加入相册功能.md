---
title: hexo主题中加入相册功能
date: 2017-02-21 22:25:30
tags: hexo
---

前段时间一个朋友问我：怎么给自己的博客加入相册功能？我还真被问住了，因为搭建博客的初衷是写一些文字性的东西，并没有考虑过“相册”这回事。但被朋友问到后，仔细想了想，给博客加入相册功能还是挺不错的，可以和大家分享一些自己喜欢的图片。于是参考了网上的相关资料，最终实现了一个简单的相册功能。现把实现过程记录下来，以供大家参考。

主要是参考了[这篇文章](http://www.cnblogs.com/xljzlw/p/5137622.html),但这篇文章的作者使用的是`yilia`主题，该主题已实现了相册功能。而我使用的是[kiddochan](https://github.com/hsihohuang/kiddochan)主题，这个主题并没有实现相册功能。因此，我的实现过程和我参考的那篇文章也略有不同，但整体流程还是类似的。

## 新建相册页面

`hexo new page “photos”`

执行上面的命令，会在`source`文件夹中生成一个`photo`文件夹，打开`photo`文件夹中的`index.md`文件，修改内容如下：

    ---
    title: 相册
    ---

    <link type="text/css" href="/css/photo.css" rel="stylesheet">
    <link type="text/css" href="/fancybox/jquery.fancybox.css" rel="stylesheet">

    <div class="photos">
      <section class="archives album">
        <ul class="img-box-ul"></ul>
      </section>
    </div>

其中，'photo.css'用来控制相册样式，由于我使用的主题并没有实现相册功能，因此需要另写一个样式来控制相册的展示。

## 配置主题

在主题的配置文件`_config.yml`的menu下添加一个相册的菜单：

    menu:
      主页: /
      归档: /archives
      关于: /about
      相册: /photos

## 存放图片

### 分析

首先图片不能放在`source`文件夹中，因为需要编译成静态文件，非常慢。那么该放到哪里好呢？

在使用hexo搭建博客时，我们都会在github上会建立一个名为`username.github.io`的仓库，而且仓库只存储了整个项目的一部分。所以，一般我们都会在github上另建一个仓库，用来存储整个博客项目的文件对博客进行备份，这样就能在不同的电脑上写博客了。因此，我们可以在根目录下新建一个`photos`文件夹用来存放相册需要展示的图片文件，这样图片的数量就不受限制了。

### 存放图片

在博客的根目录下新建一个`photos`的文件夹，里面存放你想要展示的图片文件。然后把整个博客项目部署到github上，才能访问到图片的线上地址。

## 获取图片

那么多图片的文件名该怎么获取呢？由于原生`js`不能很好的处理文件，因此不能直接在js文件中获取图片的文件名。但我们可以新建一个基于nodejs的js文件用来读取所有图片，并把所有的图片名称放在一个json文件中供相册页面的js文件使用。

在`source\photo`文件夹中新建一个`tool.js`文件，内容如下：

    "use strict";
        const fs = require("fs");
        const path = "../../photos";

        fs.readdir(path, function (err, files) {
            if (err) {
                return;
            }
            let arr = [];
            (function iterator(index) {
                if (index == files.length) {
                    fs.writeFile("output.json", JSON.stringify(arr, null, "\t"));
                    return;
                }

                fs.stat(path + "/" + files[index], function (err, stats) {
                    if (err) {
                        return;
                    }
                    if (stats.isFile()) {
                        arr.push(files[index]);
                    }
                    iterator(index + 1);
                })
            }(0));
        });

通过`node tool.js`命令运行该文件，会在`source\photos`下生成一个`output.json`文件:

    [
    	"IMG_20161005_164625.jpg",
    	"IMG_20161005_165430.jpg",
    	"IMG_20161005_165817.jpg",
    	"IMG_20161005_172129.jpg",
    	"IMG_20161006_130939.jpg",
    	"IMG_20161006_132426.jpg",
    	"IMG_20161006_132836.jpg",
    	"IMG_20161116_141430.jpg",
    	"IMG_20161116_150715.jpg",
    	"IMG_20161116_155406.jpg",
    	"IMG_20161118_124950.jpg",
    	"mmexport1479264425744.jpg",
    	"mmexport1479264435676.jpg"
    ]

之后每次添加了新的图片，都要运行`node tool.js`来更新`output.json`文件的内容。

## 生成相册页的代码

在`myBlog\themes\kiddochan\source\js`（myBlog为我的博客根目录，kiddochan为我使用的主题，替换成相应的即可）文件夹下新建一个photo.js，代码如下：

    //相册功能 2017-02-20 add
    var page = 1;
    var offset = 20;

    function photoShow(page ,data){
      var begin = (page - 1) * offset;
      var end = page * offset;
      if (begin >= data.length) return;
      var html, li = "";
      for (var i = begin; i < end && i < data.length; i++) {
          li += '<li><div class="img-box">' +
              '<a class="img-bg" rel="example_group" href="https://raw.githubusercontent.com/fantasticZhang/blog-back-up/master/photos/' + data[i] + '?raw=true"></a>' +
              '<img lazy-src="https://raw.githubusercontent.com/fantasticZhang/blog-back-up/master/photos/' + data[i] + '?raw=true" />' +
              '</li>';
      }

      $(".img-box-ul").append(li);
      $(".img-box-ul").lazyload();
      $("a[rel=example_group]").fancybox();
    }

    function photoScroll(data){
      $(window).scroll(function() {
          var windowPageYOffset = window.pageYOffset;
          var windowPageYOffsetAddHeight = windowPageYOffset + window.innerHeight;
        //  var sensitivity = 0;
          var offsetTop = $(".photos").offset().top + $(".photos").height();
          if (offsetTop >= windowPageYOffset && offsetTop < windowPageYOffsetAddHeight) {
              photoShow(++page, data);
          }
      })
    }

    var photoInit = function() {
        $.getJSON("/photos/output.json", function (data) {
            photoShow(page, data);

            photoScroll(data);
        });
    }

  其中，`https://raw.githubusercontent.com/fantasticZhang/blog-back-up/master/photos/' + data[i] + '?raw=true"`是上传至github的图片链接。

  该文件读取json文件，将图片的线上url拼接起来渲染在相册页面上。

  在`myBlog\themes\layout\_patial\after_footer.ejs`文件中加入了如下代码：

      <!--相冊功能-->
      <script src="/js/jquery.lazyload.js"></script>
      <script src="/js/photo.js"></script>

      $(document).ready(function(){
        //相册功能 2017-02-20 add
        if($(".photos").length) {
            photoInit();
         }
      });

根据实际所使用的主题，添加到相应位置即可。

最后，运行`hexo g`、`hexo d`等相应命令，相册功能就实现了。
