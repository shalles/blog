---
layout: post
category : [Tools,Fiddler,Translate]
title : "fiddlerScript 网络会话列表"
tagline: "伪翻译"
tags : [fiddler, 网络调试, hack, FE, 调试神器, 调试线上代码, fiddlerScript]
excerpt: 在fiddlerScript让你的fiddler更贴心, fiddlerScript之网络会话列表
---
{% include JB/setup %}

##Customize Web Sessions List

####定制Fiddler的web session列表，通过在FiddlerScript的OnBeforeRequest（除非特殊说明）函数中增加规则

For example:

####在会话列表的 Custom列中显示原始的request时间

```js
    oSession["ui-customcolumn"] += DateTime.Now.ToString("h:mm:ss.ffff ");
```

####在会话列表的 Custom列中显示任意的 Set-Cookie 头（添加到OnBeforeResponse）

```js
    oSession["ui-customcolumn"] = oSession.oResponse["Set-Cookie"];
```

####标记所有发送cookies的request请求为红色，并在Custom列中显示对应值，当然你可以标记为绿色

```js
    if (oSession.oRequest.headers.Exists("Cookie")) 
    {
    oSession["ui-color"]="red";
    oSession["ui-customcolumn"] = oSession.oRequest["Cookie"];
    }
    else
    oSession["ui-color"]="green";
```

####隐藏会话列表中request请求包含.gif的会话记录

```js
    if (oSession.url.EndsWith(".gif")){
        oSession["ui-hide"]="hiding image requests";  //String value not important
    }
```

####隐藏已完成并响应体是图片的会话记录（在OnBeforeResponse中）

```js
    if (oSession.oResponse.headers.ExistsAndContains("Content-Type", "image/")) {
        oSession["ui-hide"] = "hiding images"; // String value not important
    }
```

####隐藏我不关注的请求域的会话记录

```js
    if (!oSession.HostnameIs("domainIcareabout.com")){
        oSession["ui-hide"] = "hiding boring domains"; // String value not important
    }
```

####取消隐藏任何返回404的响应的会话项（OnBeforeResponse中）

```js
    if (oSession.responseCode == 404){
        oSession.oFlags.Remove("ui-hide");
    }
```

####标记所有带有服务端发送cookie的会话记录（OnBeforeResponse中）

```js
    if (oSession.oResponse.headers.Exists("Set-Cookie") ||
      oSession.utilDecodeResponse();
      oSession.utilFindInResponse("document.cookie", false)>-1 ||
      oSession.utilFindInResponse('HTTP-EQUIV="Set-Cookie"', false)>-1){
      oSession["ui-color"]="purple"; 
    }
```

####显示重定向到本地的绘画记录（OnBeforeResponse中）

```js
    if ((oSession.responseCode > 299) && (oSession.responseCode < 308)){ 
        oSession["ui-customcolumn"] = oSession.oResponse["Location"];
    }
```

####增加一个显示图片大小信息的会话记录列项（全局作用域）注意：你必须在 `Tools > Fiddler Options > Extensions > References` 中添加 `System.drawing.dll`

```js
    public static BindUIColumn("ImageSize", 60)
    function FillImageSizeColumn(oS: Session){
     if ((oS.oResponse != null) && (oS.oResponse.headers != null))
     {
       try{
          if (!oS.oResponse.headers.ExistsAndContains("Content-Type", "image/")) return "NotAnImage";

          var oMS: System.IO.MemoryStream = new System.IO.MemoryStream(oS.responseBodyBytes);
          var i:System.Drawing.Bitmap = new System.Drawing.Bitmap(oMS);
          return (i.Width + " x " + i.Height);
          }
          catch(e) { return "err"; }
       }
     return String.Empty;
    }
```

####搜索所有的文本响应组成一个字符列表并标记会话记录（在OnBeforeRequest中)

```js
    oSession.utilDecodeResponse();

    // Create a array of strings we're looking for.
    var oFindStrings = new Array( "XMLHttp", "onreadystatechange", "readyState", "responseBody", "responseText", "responseXML", "statusText", "abort", "getAllResponseHeaders", "getResponseHeader", "setRequestHeader");

    // For each target string, check the response to see if it's present.
    var iEach=0;

    oSession["ui-customcolumn"]=String.Empty;

    for (iEach; iEach<oFindStrings.length; iEach++){
        if (oSession.utilFindInResponse(oFindStrings[iEach], false)>0) { 
          oSession["ui-color"]="purple"; 
          oSession["ui-customcolumn"] += oFindStrings[iEach]+"; "; 
        }
    }
```

