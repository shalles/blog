---
layout: post
category : [Tools,Fiddler,Translate]
title : "fiddlerScript 网络会话断点"
tagline: "伪翻译"
tags : [fiddler, 网络调试, hack, FE, 调试神器, 调试线上代码, fiddlerScript]
excerpt: 在fiddlerScript让你的fiddler更贴心, fiddlerScript之网络会话断点
---
{% include JB/setup %}

##Pause Web Sessions

####通过在FiddlerScript的OnBeforeRequest函数（除非特殊说明）中增加规则来实现暂停一个指定的会话。

For example:

####暂停（断点）所有的HTTP POST，然后手动改变POST提交的form表单数据

```js
    if (oSession.HTTPMethodIs("POST")){
      oSession["x-breakrequest"]="breaking for POST";
    }
```

####暂停（断点）所有包含‘thekeyword’的HTTP POST

```js
    if (oSession.HTTPMethodIs("POST") && (oSession.utilFindInRequest("thekeyword", true) > -1)){
    oSession["x-breakrequest"] = "keyword";
    }
```

####暂停（断点）一个请求XML文件的会话以便手动修改参数

```js
    if (oSession.url.toLowerCase().indexOf(".xml")>-1){
     oSession["x-breakrequest"]="reason_XML"; 
    }
```

####暂停一个响应返回Javascript脚本的会话以便手动修改（加到OnBeforeResponse中）

```js
    if (oSession.oResponse.headers.ExistsAndContains("Content-Type", "javascript")){
     oSession["x-breakresponse"]="reason is JScript"; 
    }
```

