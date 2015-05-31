---
layout: post
category : [Tools,Fiddler]
title : "fiddler网络调试神器（前端）——多看了你一眼"
tagline: "利其器"
tags : [fiddler, 网络调试, hack, FE, 调试神器, 调试线上代码]
excerpt: 敬请期待 预计5.23号完成
---
{% include JB/setup %}

##下面我们进一步来看看fiddler的功能

敬请期待

###菜单栏
####<a id="File"></a>File

![]({{ BASE_PATH }}/assets/images/tools/fiddler/20150518173755.jpg)

如图从上到下一次是

1.`Capture Traffic`:捕获会话通信开关 与[fiddler擦肩而过](/tools/fiddler/2015/02/08/tools-fiddler-profile/#Capturing)描述一致

2.`New Viewer`:新开一个只能导入并查看fiddler导出的会话的fiddler查看器

3.`Load/Recent archive`: 加载或打开最近导出的[`.saz`数据文件](/tools/fiddler/2015/05/18/tools-fiddler-function-list/#saz)中索引3

4.`Import/Export Sessions`:导入或导出会话数据

---

####Edit

![Edit]({{ BASE_PATH }}/assets/images/tools/fiddler/20150518173932.jpg)

如图从上到下一次是

1.`Copy/Remove/Select All`:这些就不多说了，扩展的多种操作方式看看就懂

2.`Mark`:用各种颜色标记选中会话

3.`Unlock for Editing`: 解锁选中会话的编辑功能，解锁后可以在[Inspectors](/tools/fiddler/2015/02/08/tools-fiddler-profile/#Inspectors)中自由的编辑会话内容并保存，可编辑的会话对应图标为
![可编辑会话]({{ BASE_PATH }}/assets/images/tools/fiddler/20150519141434.jpg)

4.`Find Session`:关键字匹配查找需要的session

---

####Rules

![Rules]({{ BASE_PATH }}/assets/images/tools/fiddler/20150518174001.jpg)

如图从上到下一次是

1.`Hide Image Requests/Hide CONNECTs`:隐藏图片请求/

2.<a id="automaticBreakpoint">`Automatic Breakpoint`</a>:就是[request断点和response断点](/tools/fiddler/2015/05/18/tools-fiddler-function-list/#beforeRequest)

3.<a id="customizeRules">`Customize Rules`</a>: 点击会弹出一个Fiddler ScriptEditor, 这里提供了用户通过UI界面设置和Fiddler默认参数的脚本配置，右边是Fiddler提供的一些可用的对象及方法的参考。与FiddlerScript[FiddlerScript](/tools/fiddler/2015/02/08/tools-fiddler-profile/#FiddlerScript)中提供了类似的编辑功能。比如在这里你可以看到你设置的模拟modem网速的配置是在OnBeforeReques钩子方法中添加如下代码判断，UI在`Rules->Performance->Simulate Modem Speeds`,默认情况下request请求发送是300ms/KB, response响应是150ms/KB。当然你阔以自由的修改。

1. 
    ```js
    if (m_SimulateModem) {
                // Delay sends by 300ms per KB uploaded.
                oSession["request-trickle-delay"] = "300"; 
                // Delay receives by 150ms per KB downloaded.
                oSession["response-trickle-delay"] = "150"; 
            }
    ```

    ![Custom rules Editor]({{ BASE_PATH }}/assets/images/tools/fiddler/20150519145416.jpg)

4.<a id="reqProxyAuthe">`Require Proxy Authentication`</a>: 开启代理验证，Fiddler监控到使用到代理服务的地方就会弹出验证框，你只需输入user: 1 password: 1 验证通过就继续请求，否则中断应用请求。

1. **浏览器**

    ![代理验证 浏览器]({{ BASE_PATH }}/assets/images/tools/fiddler/20150519150009.jpg)

2. **其他应用**

    ![代理验证 系统应用]({{ BASE_PATH }}/assets/images/tools/fiddler/20150519145805.jpg)


5.`Apply GZIP Encoding`:使用Gzip编码压缩

6.`Remove All Encodings`:移除所有编码

7.`Hide 304s`:隐藏返回304的会话

8.`Automaticall Authenticate`:自动验证

9.`User-Agents`:改变请求头中的User-Agent

10.<a id="Performace">`Performace`</a>:性能测试

1. 
    a. Simulate Modem Speeds 模拟Modem的网速限制网络通信

    b. Disable Caching 禁用cache功能

    c. Chache Always Fresh 保持缓存刷新

---

####Tools

![Tools]({{ BASE_PATH }}/assets/images/tools/fiddler/20150518174022.jpg)

如图从上到下一次是

1.`Fiddler Options`:

1. 

    a. <a id="General">**General**</a>

    ![General]({{ BASE_PATH }}/assets/images/tools/fiddler/20150518182733.jpg)

    一些基础的配置，我们看一下:

    `Map requests to originating application`:显示对应会话的应用程序，在会话列表中的Process项

    `If client aborts while streaming`: （有三个选项）在客户端中断的时,数据流正在传输：

    finish if Seeion is visible:（如果会话可用）继续完成

    finish downloading anyway:无论怎样继续完成下载

    close the server connection:关闭与服务端的链接

    `If protocol violations are observed`: 有三个选项：

    Do northing:不监控任何违反协议的通信;

    Warn is all errors:监控所有;

    Warn on critical errors:只监控关键的错误.

    b. <a id="HTTPS">**HTTPS**</a>

    ![HTTPS]({{ BASE_PATH }}/assets/images/tools/fiddler/20150518184321.jpg)

    捕获HTTPS通信的开关，阔以选择针对指定类型的应用（如所有程序或特指浏览器中的HTTPS通信）通过Decrypt(解码)HTTPS通信提供伪造证书，屏蔽证书验证等功能，当然可以跳过不需要处理的host。上图中的蓝色链接

    点击`Fiddler.DefaultCertificateProvider`阔以选择伪造的证书的供应商，掩盖网站正真证书等功能;

    点击`Enabled Protocols:ssl3;tls1.0`阔以增删支持的安全协议类型

    c. <a id="Connections">**Connections**</a>

    ![Connections]({{ BASE_PATH }}/assets/images/tools/fiddler/20150518184400.jpg)

    这是Fiddler的强大之处，通过配置远程和监听端口，安装Fiddler的windows可以作为一个服务提供对不同设备的通信监控。

    Fiddler listens on port: 默认监听端口8888

    Capture FTP requests:捕获FTP请求

    Allow remote computers connect: 允许远程端脑链接，在捕获其他电脑或其他系统是需要打开它。

    Reuse client connections:重用客户端链接

    Reuse server connections:重用服务端链接

    Act as system proxy on startup:以系统代理启动

    Monitor all connections:监控所有链接

    [这里可以看到移动端连接到Fiddler服务器的例子](/tools/fiddler/2015/02/08/tools-fiddler-profile/#mobile)


    d. <a id="Gateway">**Gateway**</a>

    ![Gateway]({{ BASE_PATH }}/assets/images/tools/fiddler/20150518184418.jpg)
    
    设置代理方式，默认使用系统代理；可以用WPAD自动探测代理；也可以手动设置，如上图我使用goAgent的代理，默认的8087端口

    e. <a id="Tools">**Tools**</a>

    ![Tools]({{ BASE_PATH }}/assets/images/tools/fiddler/20150518184543.jpg)

    目前支持三种功能:

    文本编辑器：这里我使用了我的sublime编辑器

    FiddlerScript编辑器:使用了默认的

    diff工具：使用了Beyond Compare


2.<a id="CompareSession">`Compare Session`</a>:

对比两个选中的会话，这个需要安装插件，点击后有提示。

3.<a id="HOSTS">`HOSTS`</a>:

管理一个独立于系统hosts文件的DNS映射工具，可以导入系统hosts文件并编辑修改

1. 
    ![Tools]({{ BASE_PATH }}/assets/images/tools/fiddler/20150519170810.jpg)

4.<a id="ConfigAutoSave">`Config AutoSave`</a>:

自动保存Fiddler的配置文件，点击后在[fiddler擦肩而过中`3/4`](/tools/fiddler/2015/02/08/tools-fiddler-profile)框中出现如下图所示的panel,可以设置自动保存的时间间隔或只保存会话的请求和响应头信息

1. 
    ![Tools]({{ BASE_PATH }}/assets/images/tools/fiddler/20150519163452.jpg)

5.<a id="sessionClipboard">`New session Clipboard`</a>:

新开一个会话窗口可以查看加载保存会话，支持拖动会话到新窗口中，选中指定会话后弹出Inspectors类似的框显示会话详细信息

1. 
    ![Tools]({{ BASE_PATH }}/assets/images/tools/fiddler/20150519171246.jpg)

6.`Stave`:

是我以前安装的一个Fiddler插件，实现现在Fiddler的AutoResponder中正则阔以实现的文件路径匹配功能

---

####右键点击选中会话 

![右键菜单]({{ BASE_PATH }}/assets/images/tools/fiddler/20150518180320.jpg)

如图从上到下一次是

1.`Decode Selected Sessions`:解码选择的会话

2.`AutoScroll Session List`:会话窗口中的会话在有心会话插入式自动滚动到最新插入的会话行

3.`Filter`:安逸些Fiddler智能提取当前会话框中的所有会话的功能特点给定的规则过滤会话

其他的前面都有说到了。