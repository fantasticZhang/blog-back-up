---
title: js修改url中某个指定参数的值
date: 2017-01-16 21:48:28
categories: 前端
tags: js
---

## location对象

location对象很特别，因为它既是window对象的属性，也是document对象的属性；也就是说，window.location和document.location引用的是同一个对象。location对象将URL解析为独立的片段，让开发人员可以通过不同的属性访问这些片段。下面是location对象的所有属性。

* hash：返回URL中的hash（#号后跟的零或多个字符），如果URL中不包含散列，则返回空字符串；例: "#contents"
* host：返回服务器名称和端口号（如果有）；例："www.baidu.com:80"
* hostname：返回不带端口号的服务器名称；例："www.baidu.com"
* href：返回当前加载页面的完整URL。而location对象的toString()方法也返回这个值；例："http://www.baidu.com"
* pathname：返回URL中的目录和（或）文件名；例："/WilleyCDA/"
* port：返回URL中指定的端口号。如果没有则返回空串。例："8080"
* portocol：返回页面使用的协议，通常是http:或https:  例："http:"
* search：返回URL的查询字符串。这个字符串以问好开头；例："?q=javascript"

## js修改url中某个指定参数的值

下面是一个利用javascript修改url中某个参数的值的办法，可以直接修改当前url中的某个参数的值而不会导致这个url中同一个参数存在多个值的情况。

    /*
    * url 目标url
    * arg 需要替换的参数名称
    * arg_val 替换后的参数的值
    * return url 参数替换后的url
    */
    function changeURLArg(url,arg,arg_val){
        var pattern=arg+'=([^&]*)';
        var replaceText=arg+'='+arg_val;
        if(url.match(pattern)){
            var tmp='/('+ arg+'=)([^&]*)/gi';
            tmp=url.replace(eval(tmp),replaceText);
            return tmp;
        }else{
            if(url.match('[\?]')){
                return url+'&'+replaceText;
            }else{
                return url+'?'+replaceText;
            }
        }
        return url+'\n'+arg+'\n'+arg_val;
    }

使用方法：

    changeURLArg('http://www.daimajiayuan.com/test.php?class_id=3&id=2','class_id',4);

结果：http://www.daimajiayuan.com/test.php?class_id=4&id=2

[参考链接](http://www.daimajiayuan.com/sitejs-17226-1.html)
