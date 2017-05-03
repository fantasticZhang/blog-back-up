---
title: HTML5简介
date: 2017-03-20 19:37:21
tags: html5
categories: 前端
---

HTML5的新特性有很多，在这里主要阐述其中一些最有趣且最重要的特性。

## 结构和内容元素

### HTML5结构元素

HTML5中新的结构元素提供了许多用于描述各种网页组成部分的方法。在这之前我们只能使用大量的`div`元素和`span`元素来设计文档结构，但现在我们能够使用更多的元素了，如`section`、`header`、`hgroup`、`footer`、`nav`、`article`、`aside`。每一种元素都有独特的用途，能够帮助我们把现代网页上最常见的区域一一区分开。

* section元素

  用来定义网站中特定的可区别的区域，按主题对内容进行分组，即将内容分组到一些具有独特主题或关注点的区域中。

* header元素

  一个网页的页眉部分，其中可以包含标题、Logo、导航及其他与页眉相关的内容。

* hgroup元素

  将标题进行分组的元素。可用于包含多个标题元素(h1~h6)，常见用法是显示带有标题和小标题的内容。

* footer元素

  一个网页的页脚部分，其中可以包含版权声明和网站制作中信息等其他与页脚相关的内容。此外，`footer`还可以包含一些相关内容的链接，也就是说它们很适合在`section`和`article`中使用。

* nav元素

  显示指向页面某一部分或网站其他页面的导航链接。最常见的用途是包含网站的主导航菜单。

* article元素

  任何独立成文且可以以其他格式重用（例如：通过RSS分发）的内容都应该置于一个`article`元素中。如：博客文章、评论、论坛帖子、新闻文章等。关于`article`元素的使用，一条很好的经验法则就是：判断其内容在RSS阅读器中是否可以作为一个整体独立存在。

* aside元素

  包含内容周围的相关内容。典型应用是引文和旁注。

### HTML5内容元素

* figure元素

  `figure`元素的一个典型用途是包含图像、代码和其他内容对主内容给出某方面的说明。`figure`元素中的内容应该可以从主内容中删除而不会破坏主内容。

* figcaption元素

  有一些注解内容需要使用一个简短的标题，通常这些内容是显示在原始内容的上下文之外的。要在`figure`元素中加入标题需要使用`figcaption`元素。

* mark元素

  那些突出显示以表示引用的内容应该包含在`mark`元素中。也就是说在一段引文中对原作者没有加以强调的一句话进行突出显示。另一个用途是突出显示与用户当前活动相关的内容，例如可以用`mark`元素来突出显示用户在搜索中使用的关键词。

  注：`em`或`strong`表示原作者认为重要或需要强调的内容；`mark`表示某人在一个不同的上下文中引入原作者的话时认为重要的内容，或是用户活动的结果。

* time元素

  在内容中显示时间或日期时用该元素，所使用的任何时间都必须采用24小时格式。该元素还可以包含两个属性:`datetime`，表示在元素中指定的确切日期和时间；`pubtime`，表示文章或整个文档发布时`time`元素所指定的日期和时间。

## 表单

### 浏览器验证

HTML5表单具有浏览器内置的验证机制。默认情况下，如果浏览器支持HTML5，则表单验证都是启用的，如果验证失败会显示一条友好的警告信息。可以在`form`元素中添加`novalidate`属性来关闭验证。

### 输入类型

HTML5增加了一些新的输入类型，使得我们现在能够定义电子邮件地址、电话号码和URL的输入框等。新增的输入类型有：

* color：定义拾色器；
* date：定义日期字段（带有calendar控件）；
* datetime：定义日期字段（带有calendar和time控件）；
* datetime-local：定义日期字段（带有calendar和time控件）；
* month：定义日期字段的月（带有calendar控件）；
* week：定义日期字段的周（带有calendar控件）；
* time：定义日期字段的时分秒（带有time控件）；
* email：定义用于e-mail地址的文本字段；
* number：定义带有spinner控件的数字字段；
* range：定义带有slider控件的数字字段；
* search：定义用于搜索的文本字段；
* tel：定义用于电话号码的文本字段；
* url：定义用于URL的文本字段。

### 输入属性

除了输入类型，HTML5中还为`input`元素增加了许多其他的属性：autocomplele、autofocus、form、formaction、formenctype、formmethod、formnovalidate、formtarget、height、width、list、max、min、multiple、pattern、placeholder、required、step。

## 媒体元素

HTML5出现之前，如果我们想在浏览器中播放媒体需要借助外部插件和应用程序，浏览器本身不支持图形处理。而HTML5的一个核心目标是改进媒体支持，引入了新的媒体元素（如`audio`、`video`），使得浏览器不需要使用外部插件就能够直接播放媒体。而`canvas`元素使浏览器能够直接创建和处理图像和图形。由于`canvas`在之前的博客中已经单独介绍过，因此下面简单介绍一下`audio`和`video`。

### audio元素

音频播放器，属性有：`src`、`controls`、`autoplay`、`loop`、`preload`。可使用这些属性对`audio`元素进行细粒度的控制。

目前，主流浏览器对`audio`元素的支持各不相同，有多种音频编码器，其中最常用的的格式是MP3和OGG。可以使用如下方式实现跨浏览器HTML5音频支持。

    <audio  controls >
        <source src="sound.ogg">
        <source src="sound.mp3">
    </audio>

### video元素

视频播放器，除具有`audio`元素的所有属性外，还有属性：`height`、`width`、`muted`、`poster`。

可以采用与`audio`元素同样的方式来实现跨浏览器HTML5视频支持，即使用`source`元素。

## HTML5页面的结构

前面几节简单介绍了HTML5的一些新特性，下面是一个简单的HTML5页面。

    <!DOCTYPE html>
    <html>
    	<head>
    		<title>A basic HTML5 blog homepage</title>
    		<meta charset="utf-8">
    		<!-- CSS and JavaScript to go here -->
    	</head>

    	<body>
    		<header>
    			<!-- Website name and navigation -->
    			<h1>My amazing blog</h1>

    			<nav>
    				<ul>
    					<li><a href="/">Home</a></li>
    					<li><a href="/archive/">Archive</a></li>
    					<li><a href="/about/">About</a></li>
    					<li><a href="/contact/">Contact</a></li>
    				</ul>
    			</nav>
    		</header>

    		<section>
    			<!-- Blog articles - repeat as many times as required -->
    			<article>
    				<header>
    					<hgroup>
    						<h1><a href="/blog/first-post-link/">Main heading of the first blog post</a></h1>
    						<h2>Sub-heading of the first blog post</h2>
    					</hgroup>
    					<p>Posted on the <time pubdate datetime="2010-10-30T13:08">30 October 2010 at 1:08 PM</time></p>
    				</header>

    				<p>Summary of the first blog post.</p>
    			</article>

    			<article>
    				<header>
    					<hgroup>
    						<h1><a href="/blog/second-post-link/">Main heading of the second blog post</a></h1>
    						<h2>Sub-heading of the second blog post</h2>
    					</hgroup>
    					<p>Posted on the <time pubdate datetime="2010-10-26T09:36">26 October 2010 at 9:36 AM</time></p>
    				</header>

    				<p>Summary of the second blog post.</p>
    			</article>

    			<article>
    				<header>
    					<hgroup>
    						<h1><a href="/blog/third-post-link/">Main heading of the third blog post</a></h1>
    						<h2>Sub-heading of the third blog post</h2>
    					</hgroup>
    					<p>Posted on the <time pubdate datetime="2010-10-21T17:13">21 October 2010 at 5:13 PM</time></p>
    				</header>

    				<p>Summary of the third blog post.</p>
    			</article>

    			<!-- Blog sidebar -->
    			<aside>
    				<h2>Subscribe to the RSS feed</h2>
    				<p>Make sure you don't miss a blog post by <a href="/rss">subscribing to the RSS feed</a>.</p>
    			</aside>		
    		</section>

    		<footer>
    			<!-- Copyright and other stuff -->
    			<p>My amazing blog &copy; 2010</p>
    		</footer>
    	</body>
    </html>

## 对HTML5的误解

下面的一些技术或特性并不属于HTML5，一定要注意。

* CSS3：应用样式的技术，与内容或结构无关。
* Web Fonts：自定义字体，属于CSS3。
* Geolocation：了解用户位置，一种JavaScript API，是由想要支持它的浏览器实现的。
* SVG：一种允许使用XML创建2D矢量图的语言。是一种使用XML来描述图形的完全独立的技术。
* Web Storage：新存储方法，一种JavaScript API，是由想要支持它的浏览器实现的。
* Web Workers：可在后台执行一些繁重的计算及其他高强度的任务，从而不会导致网页显示减慢或影响用户体验。一种JavaScript API，是由想要支持它的浏览器实现的。
* WebSocket：可实现双向通信。一种JavaScript API，是由想要支持它的浏览器实现的。
