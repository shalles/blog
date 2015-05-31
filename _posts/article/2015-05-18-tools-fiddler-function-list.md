---
layout: post
category : [Tools,Fiddler]
title : "Fiddler 功能索引 Index"
tagline: "利其器"
tags : [fiddler, 网络调试, hack, FE, 调试神器, 调试线上代码, fiddlerScript]
excerpt: 在fiddlerScript让你的fiddler更贴心, fiddlerScript之修改一个请求或响应
---
{% include JB/setup %}

##Fiddler 功能索引

<a id="beforeRequest"></a>**`1. request断点`**: [详细查看]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#automaticBreakpoint) [详细查看]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/#signMean) [详细查看3]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/#reqResBreakPoint) 

使应用的会话request请求被fiddler拦截，在没有删除该断点，或go跳过该断点，request请求不会发送到服务端。此时的会话信息阔以被用户编辑修改。 

<a id="afterResponse"></a>**`2. response断点`**: [详细查看1]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#automaticBreakpoint) [详细查看2]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/#signMean) [详细查看3]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/#reqResBreakPoint) 

在response响应到达客户机但未到浏览器之前打断点，在没有删除该断点，或go跳过该断点，在断点处阔以编辑修改会话内容，再返回到发起对应请求的应用程序（如浏览器）。

<a id="saz"></a>**`3. .saz数据文件`**: [详细查看]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#File)

Fiddler离线保存会话信息的文件格式，可以选中其中某些会话,然后右键保存save->selected sessions->in ArchiveZIP将选中的会话数据保存到扩展名为.saz的本地文件。以便后续或传给他人查看使用。

<a id="modemSpeed"></a>**`4. 模拟modem网速`**: [详细查看1]({{ BASE_PATH }}/tools/fiddler/translate/0000/04/11/Translate-fiddler-script-a/#SimulateModem) [详细查看2]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#Performace)

限制response响应到客户端应用的速度，模拟猫的传输速度以测试性能或其他与网速相关的开发测试

<a id="catchHttps"></a>**`5. 捕获HTTPS会话`**: [详细查看]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#HTTPS)

打开Tools->Fiddler Options->HTTPS 

<a id="catchOtherPlatform"></a>**`6. 捕获其他平台的会话`**: [详细查看]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#Connections)

通过配置虚拟机网络设置，捕获其他系统平台的会话 

<a id="catchMobile"></a>**`7. Fiddler协助移动设备开发调试`**: [详细查看]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#Connections)

通过配置移动端网络代理到Fiddler所在的windows系统IP并指定端口号与Fiddler监听端口号相同即可完成Fiddler监听移动设备的通信 

<a id="debugOnlinePage"></a>**`8. Fiddler用本地代码调试线上页面`**: [详细查看]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#AutoResponder)

通过Fiddler把线上页面的资源uri转到本地代码实现  

<a id="changeRequest"></a>**`9. 不用打断点更改request请求头`**: [详细查看1]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/#FiddlerScript) [详细查看2]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#customizeRules) [详细查看3]({{ BASE_PATH }}/tools/fiddler/translate/0000/04/11/Translate-fiddler-script-a/) 

通过脚本编程改变会话信息，在OnBeforeRequest()中 

<a id="changeResponse"></a>**`10. 不用打断点更改response响应头`**: [详细查看1]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/#FiddlerScript) [详细查看2]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#customizeRules) [详细查看3]({{ BASE_PATH }}/tools/fiddler/translate/0000/04/11/Translate-fiddler-script-a/) 

通过脚本编程改变会话信息，在OnBeforeResponse()中 

<a id="requestCURD"></a>**`11. 增删改查request中请求头、表单提交等数据`**: [详细查看1]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/#Inspectors) [详细查看2]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/#Filters)

在Inspectors中或 Filter中 

<a id="responseCURD"></a>**`12. 增删改查response中响应头和响应体的数据`**: [详细查看1]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/#Inspectors) [详细查看2]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/#Filters)

在Inspectors中或 Filter中 

<a id="sysProxy"></a>**`13. 配置系统代理和其他代理`**: [详细查看]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#Gateway)

通过配置Gateway到指定的代理服务商实现 


<a id="createPost"></a>**`14. 创建自定义方法、数据、header的请求`**: [详细查看]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile/#Composer)

在Composer中创建各种请求 

**`. `**:<a id="more"></a>更多功能需要补充



