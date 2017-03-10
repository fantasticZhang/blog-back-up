---
title: 实例demo之Canvas小动画
date: 2017-03-10 10:29:45
tags: [demo,Canvas,html5]
categories: 前端
---

使用Canvas实现的一个动画效果：一个简单的太空场景，在太空深处有一个动态的小行星群，这里每个小行星的颜色是随机的。点击`Start`按钮，行星开始运动，点击`Stop`按钮，行星将会静止。

<link type="text/css" href="/css/asteroids.css" rel="stylesheet">

<canvas id="myCanvas" height="500"  width="850">

</canvas>
<div id="myButtons">
    <button id="start">Start</button>
    <button id="stop">Stop</button>
</div>

<script type="text/javascript" src="/js/jquery-2.0.3.min.js"></script>
<script type="text/javascript" src="/js/asteroids.js"></script>
