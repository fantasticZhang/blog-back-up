---
title: Grid布局
date: 2017-02-18 20:08:36
categories: 前端
tags: CSS
---

# 概述

Grid布局（又称网格布局）是指：以规则的网格阵列来指导和规范网页中的版面布局以及信息分布。

# CSS Grid布局

## 简介

CSS Grid布局是一种二维网格布局系统，旨在彻底改变基于网格的用户界面的设计。目前，CSS Grid布局仅仅是一个W3C工作草案，因此还没有被任何浏览器默认支持。在Chrome中体验Grid布局的方法：打来浏览器并输入chrome://flags，找到”experimental web platform features”（实验性网络平台功能）,启用并重启浏览器。Opera和Firefox也支持CSS Grid布局，具体启用方法可参考相关网络资料。

## 常用术语

在整个Grid布局中常用到的术语如下：

* 网格容器（Grid Container）：所有网格项的父元素；
* 网格项（Grid Item）：网格容器的直接子元素；
* 网格线（Grid Line）：组成网格的分界线；
* 网格轨道（Grid Track）：两个相邻的网格线之间为网格轨道，即网格的行或列；
* 网格单元（Grid Cell）：网格轨道相交的区域；
* 网格区域（Grid Area）：由任意数量的网格单元组成；
* 间距（Gutters）：两个网格轨道之间的区域。

## 设置在网格容器上的属性

* display：属性值有：grid、inline-grid、subgrid；
* grid-template-columns：用一组值来设置列的大小；
* grid-template-rows：用一组值来设置行的大小；
* grid-template-areas：通过获取网格项中的grid-area属性值来定义网格模板，用'.'代表空网格单元；
* grid-column-gap：网格单元列间距；
* grid-row-gap：网格单元行间距；
* grid-gap：网格单元间距，是grid-column-gap和grid-row-gap的简写；
* justify-items：垂直于列网格线对齐，适用于网格容器中的所有网格项。属性值有：start、end、center、stretch；
* align-items：垂直于行网格线对齐，适用于网格容器中的所有网格项。属性值同上；
* justify-content：在总网格区域大小小于网格容器时，使用该属性设置网格的对齐方式（垂直于列网格线对齐）。属性值有：start、end、center、stretch、space-around、space-between、space-evenly；
* align-content：在总网格区域大小小于网格容器时，使用该属性设置网格的对齐方式（垂直于行网格线对齐）。属性值同上；
* grid-auto-columns：设置隐式网格轨道（列）的大小，当定位网格项超出网格容器范围时，将自动创建隐式网格轨道；
* grid-auto-rows：设置隐式网格轨道（行）的大小；
* grid-auto-flow：在没有设置网格项的位置时，这个属性控制网格项的排列方式。属性值有：row、column、dense；
* grid：一种简写形式，设置网格容器所有属性。属性值有：none，(grid-template-rows) / (grid-template-columns)，(grid-auto-flow) [(grid-auto-rows) [ /(grid-auto-columns)]]。

## 设置在网格项上的属性

* grid-column-start：通过网格线来定义网格项的列开始位置；
* grid-column-end：通过网格线来定义网格项的列结束位置；
* grid-row-start：通过网格线来定义网格项的行开始位置；
* grid-row-end：通过网格线来定义网格项的行结束位置；
* grid-column：是 grid-column-start、grid-column-end的简写；
* grid-row：是 grid-row-start、grid-row-end 的简写；
* grid-area：定义网格项名字，以便创建模块；
* justify-self：定义单个网格项垂直于列网格线的对齐方式；
* align-self：定义单个网格项垂直于行网格线的对齐方式；

## 简单的例子

代码：

    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
            .grid_1{
                display: grid;
                grid-template-columns: 150px 20px 150px 20px 150px;
                grid-template-rows: auto 20px auto 20px auto;
            }
            .item1{
                grid-column: 1;
                grid-row: 1;
                background: blueviolet;
            }
            .item2{
                grid-column: 3;
                grid-row: 1;
                background: chocolate;
            }
            .item3{
                grid-column: 5;
                grid-row: 1;
                background: #e2e242;
            }
            .item4{
                grid-column: 1;
                grid-row: 3;
                background: #42e2b9;
            }
            .item5{
                grid-column: 3;
                grid-row: 3;
                background: #e28cc8;
            }
            .item6{
                grid-column: 5;
                grid-row: 3;
                background: #2be24c;
            }
            .item7{
                grid-column: 1;
                grid-row: 5;
                background: #c2e2da;
            }
            .item8{
                grid-column: 3;
                grid-row: 5;
                background: blueviolet;
            }
            .item9{
                grid-column: 5;
                grid-row: 5;
                background: #e2203f;
            }
        </style>
    </head>
    <body>
    <section class="grid_1">
        <div class="item1">1</div>
        <div class="item2">2</div>
        <div class="item3">3</div>
        <div class="item4">4</div>
        <div class="item5">5</div>
        <div class="item6">6</div>
        <div class="item7">7</div>
        <div class="item8">8</div>
        <div class="item9">9</div>
    </section>
    </body>
    </html>

效果图：

![](/images/2017-02-18/Grid.PNG)

以上只是对CSS Grid布局的简单介绍，比较详细的介绍可参考[这篇文章](http://peale.cn/2016/12/08/2016_12_8_grid/#more)

# Bootstrap对Grid布局的实现

## 实现原理

通过定义容器大小，平分12份，再调整内外边距，最后再结合媒体查询，就制作出了强大的响应式的栅格系统。

Bootstrap为不同的屏幕尺寸提供了不同的栅格样式。Bootstrap3中通过媒体查询@media定义了四种屏幕类型：超小屏幕设备手机（<768px，xs）、小屏幕设备平板（>= 768px，sm）、中等屏幕设备桌面（>=992px，md）、大屏幕设备桌面（>=1200px，lg）。针对四种不同的屏幕大小分别对container样式固定了尺寸（自动、750px、970px、1170px），例如：

    @media (min-width:1200){
      .container{ width:1170px; }
    }

## Bootstrap中栅格系统的主要工作原理

*	一行数据（row）必须包含在.container中，以便为其赋予合适的对齐方式和内边距（padding）。
*	使用行（row）在水平方向创建一组列（column）。
*	具体内容应当放置于列（column）内，而且只有列（column）可以作为行（row）的直接子元素。
*	内置了许多样式，可以使用像.row和.col-xs-5这样的样式来快速创建栅格布局。
*	通过设置padding从而创建列之间的间隔。然后通过为第一列和最后一列设置负的margin从而抵消掉padding的影响。
*	栅格系统中的列是通过指定1到12的值来表示其跨越的范围的。

## 涉及的CSS特性

* 列组合涉及两个CSS特性：左浮动和宽度百分比；
* 列偏移（offset）涉及一个CSS特性：margin-left；
* 列排序（pull、push）涉及三个CSS特性：左浮动、left、right。

此外，可使用Bootstrap提供的clearfix样式来清除浮动。

例：

    <div class=”row”>
    	<div class=”col-xs-6 col-sm-3”>div1：.col-xs-6 .col-sm-3</div>
    <div class=”col-xs-6 col-sm-3”>div2：.col-xs-6 .col-sm-3</div>
    <div class=”clearfix visible-xs”> </div>
    <div class=”col-xs-6 col-sm-3”>div3：.col-xs-6 .col-sm-3</div>
    <div class=”col-xs-6 col-sm-3”>div4：.col-xs-6 .col-sm-3</div>
    </div>
