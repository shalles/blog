---
layout: post
category : [Tools,Fiddler,Translate]
title : "Fiddler Quick Exec 快速通道命令行"
tagline: "伪翻译"
tags : [fiddler, 网络调试, hack, FE, 调试神器, 调试线上代码, QuickExec]
excerpt: 在fiddler QuickExec让你的fiddler更便捷, 用命令提高你的工作效率
---
{% include JB/setup %}

##Using QuickExec

####QuickExec Box

**快捷键**
按下`ALT+Q`快速定位到命令行.

`ps`:如果你当前的焦点没有在Fiddler界面,按下`CTRL+ALT+F`快速打开Fiddler.

在命令行获取焦点时按下`CTRL+I`可以快速将当前选中的会话的url填充到命令行。

####Default commands

**`Find命令`**

    ?searchtext

查找url中包含sometext的会话(session),点击`Enter`键开始查找，匹配到的会话会高亮显示。

**`按条件筛选会话`**

    >size

选择response响应大小大于size bytes的会话, 还有小于`<`,`=`,`@`

默认单位为`b`, 还有`k`

**例子** 

选择大于40000b

    >40000 

同样

选择小于5kb

    <5k

**=** 等号的后面阔以加响应状态，请求方法等

    =status
    =method

**例子** 

选择响应状态为`301`的会话，即会话列表里面的`Result项`

    =301

选择请求方法为`POST`的会话

    =POST

**@** 选择请求url中包含给定`host`的会话，点击`Enter`定位到匹配会话项

    @host

**例子** 

匹配到包含msn.com host的会话，如host为www.msn.com, login.msn.com的会话

    @msn.com 


**`bold`**

用粗体标记会话中包含指定匹配字符的会话

加参数，粗体标记 `任意需要查找的字符` 所匹配到的会话

    bold /bar.aspx

不加参数则清除前面的标记规则

    bold


**`bpafter`** 

设置`response断点`:在response响应到达客户机但未到浏览器之前打断点，(在断点处阔以编辑修改会话内容，Fiddler的一大特色功能)，下面所描述的response断点都指这个.

加参数 匹配请求URI中包含指定字符的会话

    bpafter /favicon.ico 

不加参数则清除前面的标记规则，下面有加参数和不加参数的命令同理。

    bpafter

**`bps`** 

匹配指定状态码的会话，并设置response断点

    bps 404
    bps

**`bpv` or `bpm`** 

设置`request断点`:使应用的会话request请求被fiddler拦截，在没有删除该断点，或go跳过该断点，request请求不会发送到服务端。(同样此时的会话信息可以被用户修改)

参数匹配指定request方法的会话

    bpv POST
    bpv

**`bpu`** 

匹配指定URI的会话并打request断点

    bpu /myservice.asmx
    bpu        <-- Call with no parameter to clear

**`cls` or `clear`**

清除会话列表中的记录项
    
    cls

**`dump`** 

把所有的会话列表中的会话记录打包zip到`C:\`

    dump

**`g` or `go`**

与[fiddler网络调试神器（前端）——擦肩而过]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/)中`@Go越过断点`描述的一致，清除所有被卡在断点的会话的断点，放其继续执行。

    g

**`help`**

获取帮助页

    help

**`hide`**

将Fiddler最小化显示到系统图标托盘

    hide

**`urlreplace`**

替换url，与重定向的功能类似，但这里是直接替换没有跳转，后面设置的规则会覆盖前面的规则

    urlreplace 被替换url 目标url
    urlreplace

**`start`**

注册系统代理

    start

**`stop`**

注销系统代理

    stop

**`show`**

从系统托盘回复Fiddler的显示

    show

**`select MIME`**

选择response响应体中包含指定类型的Content-Type响应头的会话

    select image
    select css
    select htm
    select HeaderOrFlag PartialValue

选择response响应体中包含指定类型的Header或SessionFlag的会话

    select ui-comments slow
    select ui-bold *

`ps`: `*` 通配符, 而`\*`转义后指代一个`*`符号

选择会话注释comments中包含一个`*`符号的会话

    select ui-comments \*

选择请求头包含Accept: html的会话

    select @Request.Accept html

选择responses中包含Set-Cookie响应头的会话

    select @Response.Set-Cookie domain


**`allbut` or `keeponly`**

隐藏除了包含指定值的Content-Type头的会话

    allbut xml
    allbut java

**`quit`**

Shutdown Fiddler.

    quit

**`!dns`** hostname

执行指定host参数的DNS查询，并把结果在log框中输出

    !dns www.example.com

    !nslookup www.example.com

**`!listen`** PORT [CERTHOSTNAME]

在另一个端口上面附加一个安全的HTTPS的监听

    !listen 8889
    !listen 4443 localhost
    !listen 444 secure.example.com
