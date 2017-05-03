---
title: JavaScript之Ajax总结
date: 2017-04-19 22:09:08
categories: 前端
tags: js
---

Ajax是对`Asynchronous JavaScript + XML`的简写。这一技术能够向服务器请求额外的数据而无需卸载页面，这样能够提高用户体验。JavaScript中实现Ajax技术的核心是XMLHttpRequest对象。

# Ajax运作的核心——XMLHttpRequest对象

XMLHttpRequest对象（以下简称XHR）由微软最早在IE5中引入，之后Firefox、Safari、Chrome和Opera都实现了相同的特性，使XHR成为了Web的一个事实标准。

IE7+、Firefox、Opera、Chrome和Safari都支持原生的XHR对象，在这些浏览器中创建XHR对象的方法：

    var xhr = new XMLHttpRequest();

IE5-IE7的版本中的XHR对象时通过MSXML库中的一个ActiveX对象来实现的，下面是一个兼容的实现方式：

    function createXHR(){
         if (typeof XMLHttpRequest != "undefined"){
             return new XMLHttpRequest();
         } else if (typeof ActiveXObject != "undefined"){   //IE7之前的版本
             if (typeof arguments.callee.activeXString != "string"){
               //会根据IE中可用的MSXML库的情况创建最新版本的XHR对象
                 var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0",
                                 "MSXML2.XMLHttp"],
                     i, len;

                 for (i=0,len=versions.length; i < len; i++){
                     try {
                         new ActiveXObject(versions[i]);
                         arguments.callee.activeXString = versions[i];
                         break;
                     } catch (ex){
                         //跳过
                     }
                 }
             }

             return new ActiveXObject(arguments.callee.activeXString);
         } else {
             throw new Error("No XHR object available.");
         }
     }

## 基本用法

    var xhr = createXHR();        
    xhr.open("get", "example.txt", false);
    xhr.send(null);

在创建XHR对象后，要调用的第一个方法是`open(type,url,async)`，该方法的作用是启动一个相应的请求。该方法接收三个参数，其中`type`用来指定发送请求的类型（'get'、'post'等），`url`请求的URL（相对于执行代码的当前页面，当然也可以使用绝对路径），`async`是一个布尔值，用来指定是否异步发送请求。注：调用`open()`方法并不会真正发送请求，只是启动了一个请求以备发送。

`send(data)`方法的作用是发送指定的请求。该方法接收一个参数`data`，即要作为请求主体发送的数据。如果不发送数据也要传入`null`，因为这个参数对某些浏览器是必需的。

## 相关属性、事件和方法

### 属性

* responseText：作为响应主体被返回的文本；
* responseXML：如果响应的内容类型为"text/xml"或"application/xml"，则这个属性中保存着包含响应数据的XML DOM文档。
* status：响应的HTTP状态。
* statusText：响应的HTTP状态的说明。
* readyState：表示请求/响应过程的当前活动阶段，在异步请求中常用到。取值如下：
    * 0：未初始化，即尚未调用`open()`方法。
    * 1：启动，即已调用`open()`方法，但尚未调用`send()`方法。
    * 2：发送，即已经调用`send()`方法，但尚未接收到响应。
    * 3：接收，已接收到部分响应数据。
    * 4：完成，即接收到全部响应数据，且已经在客户端可以用了。

### 事件

  * readystatechange：只要`readyState`属性的值由一个值变为了另一个，就会触发一次`readystatechange`事件。注：为了保证跨浏览器兼容性，需要在调用`open()`之前指定`readystatechange`事件的处理程序。下面是一个例子：


### 方法

* open(type,url,async)：启动一个相应的请求。
* send(data)：发送指定的请求。
* abort()：取消当前正在执行的请求。调用该方法后XHR对象会停止触发事件，而且也不允许访问任何与响应有关的对象属性。
* setRequestHeader(name,value)：设置自定义的请求头部。`name`为头部字段的名称，`value`为头部字段的值。该方法必须在`open()`之后`send()`之前调用。
* getResponseHeader(name)：取得响应头部指定字段的值，其中`name`为想要取得的头部字段的名称。
* getAllResponseHeaders()：取得一个包含所有头部信息的长字符串，下面是一个例子：

        var xhr = createXHR();        
        xhr.onreadystatechange = function(event){
            if (xhr.readyState == 4){
                if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
                    console.log(xhr.getAllResponseHeaders());
                } else {
                    console.log("Request was unsuccessful: " + xhr.status);
                }
            }
        };
        xhr.open("get", "/simulationplatform/router/reset", true);
        xhr.setRequestHeader("zjl", "zjl");
        xhr.send(null);

    Request Headers如下：

    ![](/images/2017-04-20/setRequestHeader.jpg)

    输出如下：

    ![](/images/2017-04-20/responseHeaders.png)

既然涉及到了HTTP的头部信息，那么我在这里也简单地讲一下。下面是一条XHR请求的Headers截图：

![](/images/2017-04-20/headers.jpg)

在发送XHR请求的同时，基本上所有浏览器都会发送下列头部信息：

* Accept：浏览器能够处理的内容类型；
* Accept-Charset：浏览器能够显示的字符集；
* Accept-Encoding：浏览器能够处理的压缩编码类型；
* Connection：浏览器与服务器之间的连接类型；
* Cookie：当前页面设置的任何cookie；
* Host：发出请求的页面所在的域；
* Referer：发出请求的页面的URI；
* User-Agent：浏览器的用户代理字符串。

关于Headers的详解，参见我的另一篇博客：{% post_link Http Header 详解 %}

## XMLHttpRequest2

XMLHttpRequest2进一步发展了XHR，但是目前并非所有浏览器都完整地实现了XMLHttpRequest2级规范，但所有浏览器都实现了它规定的部分内容。相对于XMLHttpRequest1，2的改进有：

* 新增了FormData对象来管理表单数据；
* 可以设置HTTP请求的时间限制；
* 增加了一些进度事件，能够获得数据传输的进度信息；
* 能够上传文件，并能够获取服务端的二进制数据；
* 可以发送跨域请求。

### FormData

表单数据的序列化是当前Web应用中使用最频繁的的功能之一，在XMLHttpRequest2之前，我们通常使用如下方法序列化表单数据：

    //自定义的序列化表单数据函数
    function serialize(form){        
        var parts = new Array();
        var field = null;

        for (var i=0, len=form.elements.length; i < len; i++){
            field = form.elements[i];

            switch(field.type){
                case "select-one":
                case "select-multiple":
                    for (var j=0, optLen = field.options.length; j < optLen; j++){
                        var option = field.options[j];
                        if (option.selected){
                            var optValue = "";
                            if (option.hasAttribute){
                                optValue = (option.hasAttribute("value") ?
                                            option.value : option.text);
                            } else {
                                optValue = (option.attributes["value"].specified ?
                                            option.value : option.text);
                            }
                            parts.push(encodeURIComponent(field.name) + "=" +
                                       encodeURIComponent(optValue));
                        }
                    }
                    break;

                case undefined:     //<fieldset>元素
                case "file":        //文件输入
                case "submit":      //提交按钮
                case "reset":       //重置按钮
                case "button":      //自定义按钮
                    break;

                case "radio":       //单选按钮
                case "checkbox":    //复选框
                    if (!field.checked){
                        break;
                    }
                //执行默认操作
                default:
                //每个参数的名和值必须使用encodeURIComponent()进行编码，才能放到URL的末尾
                    parts.push(encodeURIComponent(field.name) + "=" +
                        encodeURIComponent(field.value));
            }
        }        
        return parts.join("&");
    }

    function submitData(){
        var xhr = createXHR();        
        xhr.onreadystatechange = function(event){
            if (xhr.readyState == 4){
                if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
                    alert(xhr.responseText);
                } else {
                    alert("Request was unsuccessful: " + xhr.status);
                }
            }
        };
        xhr.open("post", "postexample.php", true);

        // 必须将Content-Type头部信息设置为application/x-www-form-urlencoded
        xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
        var form = document.getElementById("user-info");            
        xhr.send(serialize(form));
    }

XMLHttpRequest2级定义了FormData类型，该类型为序列化表单数据以及创建与表单相同格式的数据提供了便利。

基本的使用方法如下：

    var data = new FormData();     //创建一个FormData对象
    data.append("name","value");  //向其中添加数据

其中,`append(key,value)`接收两个参数：`key`对应表单字段的名字，`value`对应表单字段的值。此外，也可直接向FormData的构造函数中传入表单元素，这样就用表单元素的数据预先向其中填入键值对儿。此时，我们可以使用如下方法序列化表单数据：

    var xhr = createXHR();        
    xhr.onreadystatechange = function(event){
       if (xhr.readyState == 4){
           if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
               alert(xhr.responseText);
           } else {
               alert("Request was unsuccessful: " + xhr.status);
           }
       }
    };

    xhr.open("post", "postexample.php", true);
    var form = document.getElementById("user-info");            
    xhr.send(new FormData(form));

由于XHR对象能够识别传入的数据类型是FormData实例，并配置适当的头部信息，所以此时不用明确设置请求头部了。

支持FormData的浏览器：FireFox 4+、Safari 5+、Chrome和Android 3+版的Webkit。

### timeout

timeout属性用来设置请求在等待响应多少毫秒后就终止。与该属性对应的事件为timeout，如果浏览器在规定的时间内没有接收到响应，那么就会触发timeout事件，并调用相应的事件处理程序。例：

    var xhr = createXHR();        
    xhr.onreadystatechange = function(event){
       try {
           if (xhr.readyState == 4){
               if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
                   alert(xhr.responseText);
               } else {
                   alert("Request was unsuccessful: " + xhr.status);
               }
           }
       } catch (ex){
           //assume handled by ontimeout
       }
    };

    xhr.open("get", "timeout.php", true);
    xhr.timeout = 1000;
    xhr.ontimeout = function(){
       alert("Request did not return in a second.");
    };        
    xhr.send(null);

### 进度事件

* loadstart：在接收到响应数据的第一个字节时触发；
* progress：在接收响应期间不断触发；
* error：请求发生错误时触发；
* abort：调用abort()方法请求被终止时触发；
* load：在接收到完整的响应时触发；
* loaded：通信完成或触发error、abort、load事件后触发。

#### load事件

用来代替readystatechange事件，响应接收完毕后就会触发load事件，因此也就没必要检查readyState属性的值了。

#### progress事件

实例：

    var xhr = createXHR();        
    xhr.onload = function(event){
        if ((xhr.status >= 200 && xhr.status < 300) ||
                xhr.status == 304){
            console.log(xhr.responseText);
        } else {
            console.log("Request was unsuccessful: " + xhr.status);
        }
    };
    xhr.onprogress = function(event){
        if (event.lengthComputable){
          console.log("Received " + event.position + " of " + event.totalSize + " bytes");
        }
    };
    xhr.open("get", "altevents.php", true);
    xhr.send(null);

该事件会在浏览器接收新数据期间周期性地触发。`onprogress`事件处理程序会接收到一个`event`对象，该对象包含3个额外属性：`lengthComputable`表示进度信息是否可用，布尔值；`position`表示已经接收的字节数；`totalSize`表示根据`Content-Length`响应头部确定的预期字节数。通过该事件可在文件下载期间实现进度指示器。

文件上传也有progress事件，但上传的progress事件属于XMLHttpRequest.upload对象。

### 上传文件

实例：

    //html
    <input type="file" id="myFile" multiple="multiple">
    //js
    var xhr = createXHR();                
    var files = document.getElementById("myFile").files;
    var data = new FormData();
    for(var i = 0;i < files.length;i++){
        data.append('files[]',files[i]);
       console.log(files[i]);
    }
    xhr.open("post", "postexample.php", true);
    xhr.send(data);

### 其他

XMLHttpRequest2级还提供了`overrideMimeType()`方法，用于重写XHR响应的MIME类型。

# 跨域资源共享CORS（Cross-Origin Resource Sharing）

CORS的基本思想是：在访问跨源资源时，浏览器和服务器通过使用自定义的HTTP头部进行沟通，以决定请求或响应是否被允许。

发送请求方必须附加一个额外的`Origin`头部，其中包含请求页面的源信息（协议、域名、端口），服务器根据这个头部信息来决定是否给予响应。例：

    Origin: http://simulation.gtensor.com

如果服务器认为该请求可以接受，就在`Access-Control-Allow-Origin`头部中回发相同的源信息。如果回发的是`*`，表示是公共资源。例如：

    Access-Control-Allow-Origin: http://simulation.gtensor.com

如果没有这个头部信息，或者有这个头部信息但源信息不匹配，浏览器就会驳回请求。请求和响应都不包含cookie信息。

## CORS的实现

### IE中实现CORS

IE8中引入了XDomainRequest（简称XDR）对象来实现跨域通信。XDR的使用和XHR基本相似，但XDR的对象方法只接受两个参数：请求的类型和URL，这是因为所有XDR请求都是异步执行的，不能用它来创建同步请求。例：

    var xdr = new XDomainRequest();
    xdr.onload = function(){
       alert(xdr.responseText);
    };
    xdr.onerror = function(){
       alert("Error!");
    };

    //you'll need to replace this URL with something that works
    xdr.open("get", "http://www.baidu.com");
    xdr.send(null);    

### 其他浏览器中实现CORS

其他浏览器（Firefox、Safari、Chrome等）都通过XMR对象实现了对原生CORS的支持，只需在`open()`方法中传入**绝对URL**即可进行跨域请求。

### XDR和XHR的异同

为了能实现安全可靠的CORS，XDR对象和跨域XHR对象都有一些限制，以下是它们限制的异同。

相同之处：

* 不能发送和接收cookie；
* 不能设置自定义头部；
* 不能访问响应头部信息。

不同之处：

* XDR只支持get和post请求；
* XDR只支持异步请求，而跨域XHR支持异步请求和同步请求；
* 跨域XHR对象可以访问`status`和`statusText`属性。

两个对象的共同属性、方法和时间：responseText属性；open()、send()、abort()方法；onerror、onload事件。

### 跨浏览器CORS的实现

XHR对象有一个`withCredentials`属性用来指定某个请求是否发送凭据（`true`表示发送，`false`表示不发送）。默认情况下跨源请求是不发送凭据的。如果服务器接受带凭据的请求那么会用如下HTTP头部来响应：`Access-Control-Allow-Credentials:true`。如果发送的是带凭据的请求，但服务器的响应中没有包含这个头部，那么浏览器就不会把响应交给JavaScript。此时，  `responseText`是空串，`status`是0，此外还会调用`onerror()`事件处理程序。

检测XHR是否支持CORS的一个简单方法是：检查XHR是否存在`withCredentials`属性，之后在结合检测XDR对象是否存在，就能实现一个跨浏览器的CORS了。下面是代码：

    function createCORSRequest(method,url){
      var xhr = new XMLHttpRequest();
      if("withCredentials" in xhr){
        xhr.open(method,url,true);
      }else if(typeof 'XDomainRequest' != "undefined"){
        xhr = new XDomainRequest();
        xhr.open(method,url);
      }else{
        xhr = null;
      }
      return xhr;
    }

以下是使用XHR进行的一个跨域请求的截图：

![](/images/2017-04-21/CORS-XHR.jpg)

## 其他跨越技术

### 图像Ping

实现原理：一个网页可以从任何网页中加载图像，而不用担心跨域问题。因此可以通过动态创建图像来实现跨域请求，并通过所创建图像的`onload`和 `onerror`事件处理程序来确定是否接收到了响应。例：

    var img = new Image();
    img.onload = img.onerror = function(){
      alert("Done!");
    }
    img.src = "http://www.example.com/test?name=fantasticZhang";

图像Ping是与服务器进行简单、单向的跨域通信的一种方式。请求的数据通过查询字符串形式发送，响应可以是任意的内容。使用图像Ping时浏览器得不到任何具体的数据，但可以通过侦听`load`和`error`事件来知道响应是什么时候接收到的。

缺点：只能发送GET请求；无法访问服务器的响应文本。 用途：跟踪用户点击页面次数、跟踪动态广告曝光次数等。

### JSONP（JSON with padding）

实现原理：动态创建`<script>`元素，并为`src`属性指定一个跨域URL。例：

    function handleResponse(response){
      alert(response.name);
    }

    var script = document.createElement('script');
    script.src = "http://www.example.com/test?callback=handleResponse";
    document.body.insertBefore(script,document.body.firstChild);

JSONP由两部分组成：回调函数和数据。回调函数是当响应到来时应该在页面中调用的函数，回调函数的名字在请求中指定，例如上面例子中的`handleResponse`;数据就是传入回调函数中的JSON数据。

与图像Ping相比它的优点：支持双向通信，能够直接访问响应文本。

缺点：JSONP是从其他域中加载代码执行的，因此如果其他域不安全，响应中可能会夹杂恶意代码；很难确定JSONP请求是否失败。

# Ajax的进一步扩展——Comet

Ajax是一种从页面向服务器请求数据的技术，而Comet则是一种服务器向页面推送数据的技术。使服务器几乎能够实时地向客户端推送数据。实现Comet的手段主要有两个：**长轮询**和**流**。
## 长轮询

首先说一下传统轮询，即短轮询。短轮询是浏览器定时向服务器发送请求，看有没有更新的数据，服务器在接收到请求后立即响应，无论数据是否有效。时间线如下图所示：

![](/images/2017-04-21/短轮询.PNG)

而长轮询则是对短轮询的改进。页面发起一个请求，之后服务器一直保持连接打开，直到有数据可以发送。浏览器在接收完数据后关闭连接，并随即又发起一个到服务器的新请求。时间线如下：

![](/images/2017-04-21/长轮询.PNG)

下面是使用XHR实现长轮询的一个例子：

    //html
    <input type="button" value="点我发起长轮询" id="btn" onclick="long()">
    //js
    function long(){
       var xhr = new XMLHttpRequest();
       xhr.onload = function(){
           if((xhr.status>=200&&xhr.status<300) || xhr.status==304){
               console.log(xhr.responseText);
               var btn = document.getElementById("btn");
               btn.click();
           }else{
               console.log("error:"+xhr.status);
           }

       }
       xhr.open("get","http://www.example.com",true);
       xhr.send(null);
    }

## HTTP流

HTTP流是在页面的整个生命周期内只使用一个HTTP连接。即浏览器向服务器发送一个请求，服务器保持连接打开，然后周期性地向浏览器发送数据。

使用XHR对象实现流的代码如下所示：

    function createStreamingClient(url, progress, finished){        

        var xhr = new XMLHttpRequest(),
        received = 0;
        xhr.onreadystatechange = function(){
            var result;

            if (xhr.readyState == 3){

                //只取得最新数据并调整计数器
                result = xhr.responseText.substring(received);
                received += result.length;

                //调用回调函数处理最新数据
                progress(result);

            } else if (xhr.readyState == 4){
                finished(xhr.responseText);
            }
        };
        xhr.open("get", url, true);
        xhr.send(null);
        return xhr;
    }

    var client = createStreamingClient("streaming.php", function(data){
                    alert("Received: " + data);
                 }, function(data){
                    alert("Done!");
                 });


思路：随着从服务器不断接收数据，`readyState`的值会周期性的变为3，因此只需通过侦听`readystatechange`事件并检测`readyState`的值是否为3即可。当`readyState`值变为3时，`responseText`属性中保存着接收到的所有数据，因此需要比较此前接收到的数据并决定从什么位置开始取得最新的数据。

## SSE（Server-Sent Event,服务器发送事件）

SSE是一种实现Comet交互的浏览器API，通过`EventSource`对象实现。支持短轮询、长轮询和HTTP流，能在断开连接时自动确定何时重新连接。

SSE API可创建到服务器的单向连接，服务器通过这个连接可以发送任意数量的数据。服务器响应的**MIME**类型**必须**是`text/event-stream`，而且是浏览器中的JavaScript API能解析格式输出。例：

    var source = new EventSource("myEvent.php");
    source.onmessage = function(event){
      var data = event.data;
      //处理数据
    }

* 在创建EventSource对象时传入的URL必须与创建对象的页面**同源**；
* `EventSource`的实例有一个`readyState`属性：值`0`表示正在连接到服务器，值`1`表示打开了连接，值`2`表示关闭了连接；
* 三个事件。`open`事件：在建立连接时触发，`message`事件：在从服务器接收到新事件时触发，`error`事件：在无法建立连接时触发；
* 服务器发回的数据以字符串的形式保存在`event.data`中；
* 可通过调用`close()`方法来强制立即断开连接并不再重新连接。

## Web Sockets

Web Sockets是一种与服务器进行全双工、双向通信的信道。在JavaScript中创建了Web Socket之后，会有一个HTTP请求发送到服务器以发起连接。在取得服务器的响应后，建立的连接会使用HTTP升级从HTTP协议交换为Web Socket协议。因此，只有支持这种协议的专门服务器才能正常工作。使用Web Socket时，未加密的连接变为`ws://`，加密的连接变为`wss://`。

优点：能够在服务器和客户端之间发生非常少量的数据，而不必担心HTTP那样字节集的开销。
缺点：制定协议的时间比制定JavaScript API的时间还要长。

例：

    var socket = new WebSocket("ws://www.example.com/test");
    socket.send("hello");
    socket.onopen = function(){
      alert("Connection established.")
    };
    socket.onerror = function(){
      alert("Connection error.")
    };
    socket.onmessage = function(event){
      var data = event.data;
      //处理数据
    }
    socket.onclose = function(event){  
      alert("was clean? " + event.wasClean + " Code=" +event.code+" Reason="+event.reason);
    }

* 在创建WebSocket对象时传入的URL必须是**绝对URL**；
* `readyState`属性：值`WebSocket.OPENING`表示正在建立连接，值`WebSocket.OPEN`表示已经建立了连接，值`WebSocket.CLOSING`表示正在关闭连接，值`WebSocket.CLOSE`表示已经关闭连接；
* 四个事件。`open`事件：在成功建立连接时触发，`message`事件：在从服务器接收到新消息时触发，`error`事件：在发生错误时触发，连接不能持续，`close`事件：连接关闭时触发。
* 只能通过WebSocket连接发送**纯文本**数据；
* 使用`send()`方法向服务器发送数据；
* 使用`close()`方法关闭连接。
* `close`事件的`event`对象有单个额外的属性:`wasClean`表示连接是否已明确关闭；`code`是服务器返回的数值状态码；`reason`是服务器发回的消息。
