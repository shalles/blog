---
layout: post
category : [Tools,Fiddler,Translate]
title : "fiddlerScript 添加会话警报"
tagline: "伪翻译"
tags : [fiddler, 网络调试, hack, FE, 调试神器, 调试线上代码, fiddlerScript]
excerpt: 在fiddlerScript让你的fiddler更贴心, fiddlerScript之添加会话警报
---
{% include JB/setup %}

##Session Alerts

通过在FiddlerScript中增加规则来为指定的会话创建一个警报。

For example:

Play a sound when a file is missing. (in OnBeforeResponse)
####在一个文件返回404的时候播放一个声音（在OnBeforeResponse里添加）

```js
    if (oSession.responseCode == 404){
        FiddlerObject.playSound("C:\\windows\\media\\ding.wav");
        oSession["ui-strikeout"]="true"; 
    }
```

####在一个消息弹框里面显示HTTP  POST体（加到OnBeforeRequest里）

```js
    var oBodyString = oSession.GetRequestBodyAsString();

    if (oBodyString.Length > 0) FiddlerObject.alert(oBodyString);
```
