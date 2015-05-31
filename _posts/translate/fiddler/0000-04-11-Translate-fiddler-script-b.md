---
layout: post
category : [Tools,Fiddler,Translate]
title : "fiddlerScript 性能测试"
tagline: "伪翻译"
tags : [fiddler, 网络调试, hack, FE, 调试神器, 调试线上代码, fiddlerScript]
excerpt: 在fiddlerScript让你的fiddler更贴心, fiddlerScript之性能测试
---
{% include JB/setup %}

##Performance Testing

通过在FiddlerScript的OnBeforeResponse（除非特殊声明在OnBeforeRequest）函数中增加规则来测试应用程序的性能. 例子:

####在OnBeforeRequest中增加规则模拟 **猫** 上传

```js
    // Delay sends by 300ms per KB uploaded.
    oSession["request-trickle-delay"] = "300";
```

####模拟 **猫** 下载

```js
    // Delay receives by 150ms per KB downloaded.
    oSession["response-trickle-delay"] = "150";
```

####标记没有在客户端设置cache的内容

```js
    if (!(oSession.oResponse.headers.Exists("Expires") 
    || (oSession.oResponse.headers.ExistsAndContains("Cache-Control", "age")))
    || (oSession.oResponse.headers.Exists("Vary"))){
    {
    oSession["ui-color"]="brown"; // Use C# color strings here.
    oSession["ui-italic"]="true"; 
    }
```

####在会话列表的Custom列项中显示从请求开始到最后一个字节的数据返回所经历的时间（单位ms）

```js
    oSession["ui-customcolumn"] = oSession["X-TTLB"];
```

####在会话记录列表的 # 列项中显示收到的第一个服务端响应的字节紧随 着 # 显示直到对后一个字节（单位ms）

```js
    oSession["ui-customcolumn"] = "FB: " + oSession["X-TTFB"] + "; LB: " + oSession["X-TTLB"];
```

####增加一个 CopyTimers 内容到会话列表的菜单栏（作用域是全局作用域）

```js
    public static ContextAction("CopyTimers")
    function CopyTimers(oSessions: Fiddler.Session[]){
      if (null == oSessions){
        MessageBox.Show("Please select sessions to copy timers for.", "Nothing to Do");
        return;
      }

      var s: System.Text.StringBuilder = new System.Text.StringBuilder();

      for (var x = 0; x < oSessions.Length; x++)  {
        s.AppendFormat("{0}, {1}, {2}, {3}, {4}, {5}, {6}, {7}\r\n",
        oSessions[x].Timers.ClientConnected,
        oSessions[x].Timers.ClientDoneRequest,
        oSessions[x].Timers.ServerConnected,
        oSessions[x].Timers.ServerGotRequest,
        oSessions[x].Timers.ServerBeginResponse,
        oSessions[x].Timers.ServerDoneResponse,
        oSessions[x].Timers.ClientBeginResponse,
        oSessions[x].Timers.ClientDoneResponse
        );
      }
      Utilities.CopyToClipboard(s.ToString());
      MessageBox.Show("Done.");
    }
```

