---
layout: post
category : js
title: "前端运行时脚本错误监控"
tagline: "FE runtime script bug warning"
tags : [window.onerror, 前端脚本监控, try catch]
excerpt: 前端页面运行时脚本出错报警的问题，大体上有两种解决方案：1.使用try catch; 2.使用window.onerror;
---
{% include JB/setup %}

>这两天在关注前端页面运行时脚本出错报警的问题，大体上有两种解决方案：

>1.使用try catch;
>2.使用window.onerror;

####使用try catch
这一种方案大家一看就知道怎么实现，

```js
try{
    //all need monitoring script code
} catch(e){
    //send warning
}
```

`优点`：没有跨域问题（稍后再解释）
`缺点`：消息单一，只提供错误，如xx未定义
如图：

![控制台打出errorObj]({{ BASE_PATH }}/assets/images/warning/20150423213101.jpg)

####使用window.onerror
大概是这样的

```javascript
window.onerror = function (errorMsg, url, lineNumber, column, errorObj) {
    alert('Error: text' + errorMsg + '\nScript: ' + url + '\nLine: ' + lineNumber
    + '\nColumn: ' + column + '\nStackTrace: ' +  errorObj);
    console.log(errorObj);
    //发送警报什么的
}
```

`优点`:提供的消息全面errorMsg(错误消息), url(包含错误的文件路径) lineNumber(错误所在行) column(错误所在列) errorObj(这个各浏览器实现有区别)
如图：

![控制台打出errorObj]({{ BASE_PATH }}/assets/images/warning/20150423213143.jpg)

特别是最后一个参数errorObj这里面包含了调用栈的信息和具体错误。
如下图：控制台打出errorObj

![控制台打出errorObj]({{ BASE_PATH }}/assets/images/warning/20150423213229.jpg)

`缺点`：有跨域的问题

####这里问什么有跨域问题的出现
一般情况下为了减少cookies等我们的资源文件（这里就说js了）没有与页面放在同一个域`origin`下。在这种情况下浏览器为安全考虑会做一些限制，如在`window.onerror`中（window ）：出现脚本运行时错误时，他只提供了`errorMsg`参数的有效值（且此值的描述比`try catch`要简单返回`textScript error`），其他的则返回空。而`try catch`不受这一限制（返回的值不变但依旧描述信息不全）。

![控制台打出errorObj]({{ BASE_PATH }}/assets/images/warning/20150423213540.jpg)

####为了使用window.error的调用栈解决跨域
知道错误所在行列都不重要，因为运行时的错误很多是很难重现的，可能不同用户从不同共能过来，同样的代码参数不同也同样会引起脚本错误。
>`ps`：之前处理了一个bug，大概是这样：有的用户没有获取到某张卡的具体信息，但代码中下一步去取了卡号的子串，这样就引起了代码的运行时错误`~undefined.substr`。这种问题是不容易复现的。

这时如果有onerror监控并把调用栈和描述日志记录下来或提交报警，可以看到上面例子描述的问题解决起来不算什么，但要知道这个地方引起了多少失误那就需要花大量的时间去排查。
鉴于window.onerror的好处，我们还是要硬着头皮去解决他的跨域问题。
资源服务器：

```
res.setHeader("Access-Control-Allow-Origin", "http://localhost:8088");
```

在页面引入脚本的script标签中加入`crossorigin="anonymous"`

```html
<script crossorigin="anonymous" src="http://localhost/src/scripts/index.js"></script>
```

要是使用了外部CDN（买服务，不是自己的服务器）那就麻烦了，需要在CDN服务器上设置CORS的header
还好我们使用的是https，https下的资源一般不会放到CDN。


参考文章
https://danlimerick.wordpress.com/2014/01/18/how-to-catch-javascript-errors-with-window-onerror-even-on-chrome-and-firefox/