---
layout: post
category : [Tools,Fiddler,Translate]
title : "fiddlerScript 用户菜单"
tagline: "伪翻译"
tags : [fiddler, 网络调试, hack, FE, 调试神器, 调试线上代码, fiddlerScript]
excerpt: 在fiddlerScript让你的fiddler更贴心, fiddlerScript之用户菜单
---
{% include JB/setup %}

##Customize Menus

定制Fiddler菜单, 只需在FiddlerScript中增加全局作用域的规则. 

For example:

####增加内容菜单项来实现在Firefox中打开选择的url

```js
    public static ContextAction("Open in Firefox")
    function DoOpenInIE(oSessions: Fiddler.Session[]){ 
      if (null == oSessions){
        MessageBox.Show("Please choose at least 1 session."); return;
      }

      for (var x = 0; x < oSessions.Length; x++){
        System.Diagnostics.Process.Start("firefox.exe", oSessions[x].url);
      }
    }
```

####增加一个子菜单到这个规则菜单中并为其创建一个操作

```js
    public static RulesOption("Non-Exclusive-Test", "User-Agent") 
    var m_UANONRad: boolean = true; 
```

####创建带互斥单选radio操作的子菜单

```js
    public static RulesOption("Spoof Netscape &3.0", "User-Agent", true) 
    var m_NS3: boolean = false; 

    public static RulesOption("Spoof IE &6.0", "User-Agent", true) 
    var m_IE6: boolean = false; 

    public static RulesOption("Spoof nothing", "User-Agent", true) 
    var m_UANONE: boolean = true;
```

####创建带互斥单选radio操作的子菜单并维护对应的一个字符变量来处理比RulesOption更复杂的语法。

```js
    RulesString("&SubMenuName", true) 
    RulesStringValue(0,"Option1Name", "Option1Value")
    RulesStringValue(1,"Option2Name", "Option2Value")
    RulesStringValue(2,"&Custom...", "%CUSTOM%")
    public static var sTheOptionValue: String = null;
```

####和前面一样，但是带预选默认值

```js
    RulesString("&SubMenuName", true) 
    RulesStringValue(0,"Option1Name", "Option1Value")
    RulesStringValue(1,"Option2NameDEFAULT", "DefaultVal", true)
    RulesStringValue(2,"&Custom...", "%CUSTOM%")
    public static var sTheOptionValue: String = null;
```

####增加一个工具菜单来操作script重置

```js
    // Force a manual reload of the script file. Resets all
    // RulesOption variables to their defaults.
    public static ToolsAction("Reset Script")
    function DoManualReload(){ 
        FiddlerObject.ReloadScript();
    }
```

####增加一个工具菜单来清除所有WinINET/IE cookies和缓存文件

```js
    public static ToolsAction("Reset IE"){
      FiddlerObject.UI.actClearWinINETCache();
      FiddlerObject.UI.actClearWinINETCookies(); 
    }
```

