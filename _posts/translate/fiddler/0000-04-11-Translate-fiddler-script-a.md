---
layout: post
category : [Tools,Fiddler,Translate]
title : "fiddlerScript 修改一个请求或响应"
tagline: "伪翻译"
tags : [fiddler, 网络调试, hack, FE, 调试神器, 调试线上代码, fiddlerScript]
excerpt: 在fiddlerScript让你的fiddler更贴心, fiddlerScript之修改一个请求或响应
---
{% include JB/setup %}

##Modifying a Request or Response

用户可以在FiddlerScript的OnBeforeRequest和 OnBeforeResponse函数中修改请求头和响应头. 即前面文章中说到的[request断点和response断点实现的功能]({{ BASE_PATH }}/tools/fiddler/2015/05/18/tools-fiddler-function-list)

注意：你不可能在请求发送之前即在OnBeforeRequest函数中使用response对象的属性，因为此时他还没有被创建。
但你可以在response返回到应用程序之前即OnBeforeResponse函数中使用
request对象，只是此时对request对象的修改不会对服务端有影响了。

####<a id="addHeader"></a>添加一个请求头

```js
    oSession.oRequest["NewHeaderName"] = "New header value";
```

####删除一个响应头

```js
    oSession.oResponse.headers.Remove("Set-Cookie");
```

####改变同一服务下的一个页面到请求到一个不同页面

```js
    if (oSession.PathAndQuery=="/version1.css") {
      oSession.PathAndQuery="/version2.css";
    }
```

####指定一个服务下的所有request请求到同端口的另一个服务下

```js
    if (oSession.HostnameIs("www.example.com")) {
      oSession.hostname="test.example.com";
    }
```

####指定一个服务下的所有request请求到另一个服务下的同不端口应用

```js
    if (oSession.host=="www.example.com:8080") {
      oSession.host="test.example.com:9090";
    }
```

####指定一个服务下的所有request请求到另一个服务下 ，并包含HTTPS协议

```js
    // Redirect traffic, including HTTPS tunnels
    if (oSession.HTTPMethodIs("CONNECT") && (oSession.PathAndQuery == "www.example.com:443")) { 
        oSession.PathAndQuery = "beta.example.com:443"; 
    }

    if (oSession.HostnameIs("www.example.com")) oSession.hostname = "beta.example.com"; 
```

####模拟hosts文件，指定一个hostname到一个不同的IP地址，而不需要改变请求头的host值

```js
    // All requests for subdomain.example.com should be directed to the development server at 128.123.133.123
    if (oSession.HostnameIs("subdomain.example.com")){
    oSession.bypassGateway = true;                   // Prevent this request from going through an upstream proxy
    oSession["x-overrideHost"] = "128.123.133.123";  // DNS name or IP address of target server
    }
```

####重新指定一个单页面的请求到一个不同的页面，可以不同服务源（会改变请求图的host值）

```js
    if (oSession.url=="www.example.com/live.js") {
      oSession.url = "dev.example.com/workinprogress.js";
    }
```

####阻止客户端上传cookies

```js
    oSession.oRequest.headers.Remove("Cookie");
```

####解压并解码一个http response响应，需要的情况可以更新响应头

```js
    // Remove any compression or chunking from the response in order to make it easier to manipulate
    oSession.utilDecodeResponse();
```

####解压解码后阔以搜索并替换HTML响应体的内容

```js
    if (oSession.HostnameIs("www.qunar.com") && oSession.oResponse.headers.ExistsAndContains("Content-Type","text/html")){
      oSession.utilDecodeResponse();
      oSession.utilReplaceInResponse('<b>','<u>');
    }
```

####大小写敏感的搜索response响应体HTML

```js
    if (oSession.oResponse.headers.ExistsAndContains("Content-Type", "text/html") && oSession.utilFindInResponse("搜索内容", "value")>-1){
      //标记匹配的内容为红色
      oSession["ui-color"] = "red";
    }
```

####移除所有的`div`元素及其子元素

```js
    // If content-type is HTML, then remove all DIV tags
    if (oSession.oResponse.headers.ExistsAndContains("Content-Type", "html")){
      // Remove any compression or chunking
      oSession.utilDecodeResponse();
      var oBody = System.Text.Encoding.UTF8.GetString(oSession.responseBodyBytes);

      // Replace all instances of the DIV tag with an empty string
      var oRegEx = /<div[^>]*>(.*?)<\/div>/gi;
      oBody = oBody.replace(oRegEx, "");

      // Set the response body to the div-less string
      oSession.utilSetResponseBody(oBody); 
    }
```

####伪装你的浏览器是 GoogleBot 谷歌机器人搜索引擎

```js
    oSession.oRequest["User-Agent"]="Googlebot/2.X (+http://www.googlebot.com/bot.html)";
```

####修改请求头内容

```js
    oSession.oRequest["Accept-Language"]="he";
```

####禁用`.css`文件

```js
    if (oSession.uriContains(".css")){
     oSession["ui-color"]="orange"; 
     oSession["ui-bold"]="true";
     oSession.oRequest.FailSession(404, "Blocked", "Fiddler blocked CSS file");
    }
```

####模拟HTTP基础验证，在显示网页内容前需要请户密码验证

```js
    if ((oSession.HostnameIs("www.example.com")) && 
     !oSession.oRequest.headers.Exists("Authorization")) 
    {
    // Prevent IE's "Friendly Errors Messages" from hiding the error message by making response body longer than 512 chars.
    var oBody = "<html><body>[Fiddler] Authentication Required.<BR>".PadRight(512, ' ') + "</body></html>";
    oSession.utilSetResponseBody(oBody); 
    // Build up the headers
    oSession.oResponse.headers.HTTPResponseCode = 401;
    oSession.oResponse.headers.HTTPResponseStatus = "401 Auth Required";
    oSession.oResponse["WWW-Authenticate"] = "Basic realm=\"Fiddler (just hit Ok)\"";
    oResponse.headers.Add("Content-Type", "text/html");
    }
```

####从\Captures\Responses文件夹下加载一个文件来响应一个request请求（阔以在OnBeforeRequest或OnBeforeResponse函数中添加）

```js
    if (oSession.PathAndQuery=="/version1.css") {
      oSession["x-replywithfile"] ="version2.css";
    }
```

####<a id="SimulateModem"></a>改变模拟网速的时间（在OnBeforeRequest中）

```js
if (m_SimulateModem) {
    // Delay sends by 300ms per KB uploaded.
    oSession["request-trickle-delay"] = "300"; 
    // Delay receives by 150ms per KB downloaded.
    oSession["response-trickle-delay"] = "150"; 
}
```

#### Access-Control-Allow-Origin 添加同源响应头

```js
    // http://www.xxx.com为请求发起源。 可以使用通配符 "*" "/" ...
    oResponse.headers.Add("Access-Control-Allow-Origin", "http://www.xxx.com");
```