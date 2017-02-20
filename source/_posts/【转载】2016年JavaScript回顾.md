---
title: 【转载】2016年JavaScript回顾
date: 2016-12-30 17:07:15
categories: 前端
tags: js
---

2016年是值得纪念、奇怪的、有点高兴也有点可怕的一年，这一切取决于你怎么看。跟其他事件相比仅仅专注于javascript可能看起来没什么大不了，但对于每个web开发人员来说javascript都是工作生活中非常大的一部分。

[JavaScript的流行继续激增](http://www.modulecounts.com/)。不是每个人都喜欢这种语言，但是你很少听到十年前的嘲笑意见。就个人而言，我一直喜欢JavaScript，即使在早期，那些令人沮丧的年代。只要从C++、Java或PHP方向接近它的人首先都会感到困惑：JavaScript看起来很熟悉，但又不是。克服你的假设，你会喜欢它简单的优雅，实用性和灵活性。（比如：日期处理仍然是一个噩梦！)

JavaScript在5月份庆祝了它21岁的生日，让我们回顾下它成熟发展的这第一年...

## ECMAScript发展

ES6/2015是语言自诞生以来最重要的更新。规范花了七年才完成，但浏览器和运行时终于开始支持箭头函数，let、const，这带来了更多的乐趣。[ ES6兼容性表正在变成一个光辉的绿色](http://kangax.github.io/compat-table/es6/)。

如果你需要支持旧的浏览器，全面切换到ES6也许会有一点早。对于旧的浏览器，一年多前发布的项目。您可以用ES6到ES5编译器（如Babel），但是开发非常复杂，需要引入额外的构建步骤。

ES7/2016是更多的革命。一个令人兴奋的新功能是async，它允许异步代码以同步方式写入，而没有回调或Promises（继续困惑我）的语法复杂性。

## 渐进式Web应用程序

我最喜欢的基于JavaScript的2016年技术授予Progressive Web Apps。 PWA是在谷歌的[2015年Chrome开发者峰会](https://developers.google.com/web/shows/cds/2015/progressive-web-apps-chrome-dev-summit-2015)上宣布，但稳定的技术和工具终于在7月到达Chrome 52。 PWA允许离线优先功能，并取代片状AppCache方法。 Web应用程序终于可以和native应用程序竞争，并提供以下优势：

* 主屏幕图标
* 快速启动和自定义闪屏
* 快速执行
* 离线功能，无需互联网连接
* 网址，链接和书签
* 全屏或主题界面
* 沙盒执行
* 本地或基于云的存储
* 更少的设备空间和处理资源
* 更好的安全性（HTTPS是先决条件）
* 从任何搜索引擎轻松发现
* 请在安装前尝试
* 更简单的部署：它只是一个Web应用程序
* 没有AppStore废话：你的应用程序可以包含任何裸体画并且你绝对会渴望没有人能拿走你30％的利润！

最重要的是：任何网站或者应用程序可以在几小时内转换为PWA。步骤：

1. 在服务器上启用HTTPS。
2. 创建一个应用程序清单 - 应用程序根目录中的JSON文件，用于定义名称，颜色，图标和显示选项。
3. 创建一个Service Worker - 根目录中的JavaScript文件用于拦截网络调用,并且可以根据需要返回缓存或实时数据。

初期例子很少，但是PWA提供了一个不错的机会“动员”您的Web应用程序。虽然不能保证苹果将实施这项技术，但这并不重要，你的应用程序仍然可以在Safari中工作，只是它不会从离线执行中受益。我有一种感觉一旦Android上web体验有明显提升,苹果将鼓励支持PWA。

有关详细信息，请参阅 [Dev.Opera’s Progressive Web Apps: The definitive collection of resources](https://dev.opera.com/articles/pwa-resources/)和[Google的PWA指南](https://developers.google.com/web/progressive-web-apps/)。

## 框架固定

很难做出一个公正的判断，但是[React](https://facebook.github.io/react/)似乎在今年受到最多的关注。你也许不同意;因为这取决于你使用的是什么，你曾经浏览过的地方和你说过的话！

[Vue.js](https://vuejs.org/)已经普及，2.0版本于9月发布。

[AngularJS](https://angularjs.org/)可能已经失去了它在2015年获得的一些势头，但这可能会随着9月发布Angular 2而改变。新版本是一个完全重写;它不是向后兼容v1.0。

虽然新的框架和库是令人兴奋的，但是十年前的jQuery仍然强大。版本3.0于6月9日发布，v3.1随后于7月7日出现。该库现在以严格模式运行，支持Promise，并实现了各种修复。 （查看完整修改列表的[升级指南](http://jquery.com/upgrade-guide/3.0/)。）

在使用JavaScript的96.4％的网站上用了jQuery。与之相比，Angular是最常用的现代框架 - 占0.5％。 jQuery 1.x是最流行的版本，占93.5％的使用率。版本2.x占6.0％、版本3.x占0.5％。

我一直批评开发人员上来就直接使用jQuery。当有一个其他更合适的选择或只要一小段JavaScript就能实现需求的时候，它就会被过度使用了。然而，它提供了更浅的学习曲线，比大多数框架更灵活。其他框架或库要取代它需要很多年的时间。

## API滥用

比如电池状态API。在我2013年写JavaScript的时候，它似乎很有用;当你的应用程序检测到用户的手机即将关机的时候，到底怎样才能更好的最小化网络请求和处理呢?

不幸的是，Mozilla预估大约有6％的网站用了这个API​​，但大多数是广告客户用来检测电池状态，并跟踪用户浏览了哪些网站的域名。也有可能是某些服务商知道一个用户手机快关机绝望的时候，以此来提高某些服务的价格，

虽然这不是JavaScript或API的问题，出于隐私方面的原因，Mozilla采取了空前的手段把电池状态API从Firefox 53中删除。这不太可能出现在ios设备中,其他具有类似原因的API也包括传感器和蓝牙。这是一个不太光彩的事情：这些API都有实际的好处，我希望隐私问题可以在未来的版本得到解决。

## Node新版本

每年Node.JS会给我们带来两次发布进度，4月的6.0版本和10月的7.0版本。

目前该平台呈上升趋势，尽管W3Techs报告Node.js服务器使用率仅为0.2％，而PHP为82.3％。这些数字可能有点误导，因为Node.js即使安装了，也不一定能识别。

因为php已经开始很久了，并且现在依然是服务端最靠谱的选择。然而，Node.js正在书写自己的历史，并被所有语言信仰的开发者广泛使用。

## Yarn

我非常喜欢npm，并且认为它是Node.js工具火爆的主要原因之一。在npm上我从来没有遇到过太多的问题，但我不是在Facebook这样大的项目上工作。

Facebook的工程师在10月份发布了Yarn。它是一个新的Node.js包管理器，旨在比npm更快更稳定。它依赖于npm注册表，因此可以和npm保持完全兼容。

Tim Severien的[Yarn vs npm：你需要知道的一切](https://www.sitepoint.com/yarn-vs-npm/)。我同意他的结论：

虽然Yarn不是一个复制品，但它改善了npm几个缺陷的地方。如果npm从Yarn身上学习，并要求Facebook、谷歌和其他Yarn贡献者一起来改进npm，这样是不是很爽呢？

## 厌倦疲劳

2016年的有篇文章叫I-can't-take-this-any-more入选了Jose Aguinaga的[2016年学习JavaScript的感觉](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f#.e4rkosi1w)。亚军：dayssincelastjavascriptframework.com。

这可能是种幽默的方式以表明当前JavaScript的状态，但有一点要说明的就是，就是现在要跟上最新的趋势，框架和建议变得越来越难。当面对大量的技术方案评估时，开发人员会相互争执。

我的建议：不要试图跟上。因为这根本做不到。你今天重点关注的任何系统,明天都将被更好的东西取代。为您的项目选择一个好的方案，并坚持使用它，除非它让工作变得很糟糕。

有一个是可以确定的就是JavaScript本身。首先学习语言，并继续扩展你的知识。您的经验将帮助您了解每个框架的运作方式，以便您做出明智的选择，即便这种选择可能是完全放弃所有的框架。

新年快乐！

原文链接：[【译】2016年javascript回顾](http://cnedwan.com/2016/12/21/%E8%AF%91-2016%E5%B9%B4Javascript%E5%9B%9E%E9%A1%BE.html)
