---
layout: post
category : [Tools,Fiddler,Translate]
title : "fiddlerScript 增加会话显示项"
tagline: "伪翻译"
tags : [fiddler, 网络调试, hack, FE, 调试神器, 调试线上代码, fiddlerScript]
excerpt: 在fiddlerScript让你的fiddler更贴心, fiddlerScript之增加会话显示项
---
{% include JB/setup %}

##Add Columns to the Web Sessions List

通过在FiddlerScript中添加规则来实现在会话列表中增加新列。

###关于BindUIColumn属性

填充一个用户列项添加一个被BindUIColumn属性标记的方法。Fiddler将会在每一个会话记录上运行这个方法并去填充用户定义列项。（对于减少异常，可以确保你的方法足够健壮并确保对应对象在使用前已经存在）

For example:

####用会话请求的HTTP方法填充用户列项

```js
    public static BindUIColumn("HTTPMethod")
    function CalcMethodCol(oS: Session){
        if (null != oS.oRequest) return oS.oRequest.headers.HTTPMethod; else return String.Empty; 
    }
```

####用会话的时间token填充用户列项

```js
    public static BindUIColumn("Time Taken")
    function CalcTimingCol(oS: Session){
        var sResult = String.Empty;
        if ((oS.Timers.ServerDoneResponse > oS.Timers.ClientDoneRequest)){
            sResult = (oS.Timers.ServerDoneResponse - oS.Timers.ClientDoneRequest).ToString();
        }
        return sResult;
    }
```

####这里是BindUIColumn的四个重写方法，是的我们可以设置列项的宽，排显示序号，或是否按照序数排列

```js
    BindUIColumn(string colName)

    BindUIColumn(string colName, bool bSortColumnNumerically)

    public BindUIColumn(string colName, int iColWidth)

    public BindUIColumn(string colName, int iColWidth, int iDisplayOrder)
```

###AddBoundColumn 方法

可选择的，你也可以调用AddBoundColumn()方法：

1. 第一个参数是列名；

2. 第二个参数是列的默认宽度；

3. 第三个参数是fiddler会话标记字符串，是一个`@-前缀-请求/响应头`的名字，或者是一个返回一个字符串的js函数。

```js
    static function Main(){
      
        FiddlerObject.UI.lvSessions.AddBoundColumn("ClientPort", 50, "X-ClientPort");
        FiddlerObject.UI.lvSessions.AddBoundColumn("SentCookie1", 60, getSentCookie);
        FiddlerObject.UI.lvSessions.AddBoundColumn("SentCookie2", 60, "@request.Cookie");
        FiddlerObject.UI.lvSessions.AddBoundColumn("ReturnedCookie", 60, "@response.Set-Cookie");
    }

    static function getSentCookie(oS: Session){ 
        if (null != oS.oRequest) return oS.oRequest["Cookie"]; 
    }
```
