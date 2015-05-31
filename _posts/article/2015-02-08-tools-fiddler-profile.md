---
layout: post
category : [Tools,Fiddler]
title : "fiddler网络调试神器（前端）——擦肩而过"
tagline: "利其器"
tags : [fiddler, 网络调试, hack, FE, 调试神器, 调试线上代码]
excerpt: 一个网络相关的开发调试利器，前端调试线上代码，跟踪会话更改请求或响应信息。
---
{% include JB/setup %}

##Web Debugging
支持基于`windows`PC端`windows`、`Mac`、`Linux`系统，各种移动设备的通信调试且无浏览器限制（非`pc windows`只需多个设备与之在同一个网域下即可）。
> fiddler可以拦截并解析http/https通信，你可以查看和更改已发出的请求信息，或者在服务端返回到浏览器之前更改已返回的响应头信息，把fiddler当做一个中间人。
> 
> fiddler提供了很多方法来让你分析网络会话信息，fiddler不限于监听浏览器的会话信息，其他软件的通信业会被fiddler截获供您分析

---

##入门Fiddler

**首先**，安装Fiddler。

[下载Fiddler](http://www.telerik.com/download/fiddler)

**接下来**，配置Fiddler服务器。

Fiddler服务器就是当前安装fiddler的windows PC。

> ps: fiddler依赖.NET环境，因此目前只能在window上安装，但通过配置，其他操作系统或设备与安装fiddler的系统(fiddler服务器)同在一个网域下(简单的说可以连同一个wi-fi然后设置代理)

<a id="mobile"></a>
如在我的手机上, 代理主机名就是fiddler服务器的`计算机`名或`IP地址`，注意fiddler默认监控的端口是`8888`, [这个可以配置]({{ BASE_PATH }}/tools/fiddler/2015/02/08/tools-fiddler-profile2/#Connections)

![手机配置fiddler服务]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429140745.jpg)


某些情况下可能需要特定的配置。如下：

1. 流量类型：如HTTPS进行解密，并与通道绑定证书认证
2. 操作系统：如Windows 8中和的Mac OSX
3. 网络配置：像监视远程计算机，链接到上游代理，使用Fiddler作为一个反向代理，监控本地流量或监视拨号(dal-up)和VPN连接

**最后**，配置客户端。

客户端是Fiddler网络流量监控的源。某些客户端应用程序，操作系统，和设备可能需要特定的步骤，以发送和接收与Fiddler通信。这包括：

1. 浏览器：如火狐，Opera或IE（当发送通信到localhost）
2. 应用程序：如.NET应用程序，WinHTTP的应用，Java应用程序和PHP /cURL apps
3. 设备：如安卓，iOS的，Windows Phone 7的，和掌上电脑设备


##配置浏览器

- 配置浏览器`使用系统代理`. 例：安装过switchy VPN的 chrome [不同浏览器详情请点击](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureBrowsers);

![系统代理chrome]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429133917.jpg)

- 在Fiddler中设置：`File > Capture Traffic`。或`F12` 或点击左下角图标显示`Capturing`;

##下面我们来看看fiddler的主界面及各个功能块

![Fiddler主界面]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429131122.jpg)

菜单栏 `File Edit Rules...` 稍后一一介绍， 我们先来看看如上图对应的功能模块
在讲解之前我们先来统一一下几个词汇

|    下文使用词汇     |              解释              |
| :-----------------  | :----------------------------: |
| 会话/请求/通信记录  | fiddler如上图中2中的一项记录   |
| 指定请求/会话       | 在上图2中选中的一项会话记录    |
| [1-6]框             | 在上图中用红矩形圈住的框       |

###`1:`工具栏
从左到右

1. <a id="bubble">**a. 气泡框**</a>

    功能：如图给当前指定的会话添加一个备注

    ![气泡框]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429162811.jpg)

2. <a id="Replay">**b. Replay**</a>
    
    重复发送指定请求:
    点击该按钮 `+`

    a.无其他操作：重复发送一次指定请求

    b.按住`ctrl`：无条件的重复发送一次指定请求

    c.按住`shift`：出现如下图，输入重复次数后，fiddler按一定的频率快速的发送指定次数的指定请求
    
    ![Replay]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429164858.jpg)


3. <a id="catgoryClearSession">**c. X清除会话**</a>

    分类清除指定类型的会话记录清除

    ![X清除会话]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429170028.jpg)


4. <a id="go">**d. Go越过断点**</a>

    作用目标一个或多个加了`断点`（在讲5框中功能的时候解释）的所有会话

    ![Go]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429171333.jpg)
    
    点击`go`按键同时按住`shift`键只越过指定会话

5. <a id="Stream">**e. Stream流**</a>
    
    选中后每个通信的响应以流的形式传到客户端浏览器等

6. <a id="Decode">**f. Decode**</a>
    
    选中按钮后，每一个通信的服务端响应都会被解码，如，服务端对网页做了`gzip`压缩，那fiddler截获的时候就被解压了，可以直接在fiddler 4框中`Inspectors`选项框查看各种通信信息，如下图，没有选中会提示解码

    ![Decode]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429174206.jpg)

7. <a id="keepSessions">**g. keep xx sessions**</a>

    在2框中保持最近xx个会话记录, 选中all保存所有, 默认值Keep All sessions

    ![Decode]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429175334.jpg)

8. <a id="anyProcess">**h. Any Process**</a>
    
    在该按钮上按住鼠标左键，将鼠标拖到想要让fiddler监控的程序或浏览器的一个选项卡上以让fiddler只监控该指定的进程。默认值`Any Process`

    默认：
        ![默认]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429175744.jpg)

    选中：
        ![选中]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429180257.jpg)
    
9. <a id="Find">**i. Find**</a>

    按条件搜索会话记录并标记 可以用不同色同时标记多种匹配项

    ![Find]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429180918.jpg)

    ![Find]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429181129.jpg)![Find]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429181343.jpg)![Find]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429181422.jpg)

10. <a id="Save">**j. Save**</a>
    
    保存当前所有的会话信息。这个很有用，可以把自己按监控或调试的信息给协作者共享，共同发现问题。

11. <a id="picCut">**k. 相机截图**</a>

    单击可以倒计时`5s`屏幕截图， 按住`shift`点击立刻截屏，且通过会话的形式返回，并记录会话

    ![相机截图]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429185157.jpg)


12. <a id="timeClock">**l. 计时器**</a>
    
    单机启动，右击停止

13. <a id="openInBrowser">**m. 在浏览器中打开**</a>
    
    在指定浏览器中打开选中会话的`URL`

    ![相机截图]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429185803.jpg)


14. <a id="clearCache">**n. Clear Cache**</a>

    清除`winINET cache`， 按住`ctrl`键可清除永久`cookies`

15. <a id="TextWizard">**o. TextWizard**</a>

    一个支持多种编码解码格式的文本编辑器

    ![TextWizard]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429191250.jpg)
    
    选中`view bytes` 直接显示编码后的2二进制数据 
    点击`Encodings`在出现下图可选择多种编码格式转换到输出文件`To File`或`As Session`作为一个会话输出，可以再fiddler 会话框中查看记录

    ![TextWizard]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429191613.jpg)

16. <a id="Tearof">**p. Tearof**</a>
    
    把右边栏脱离主窗口，点×关掉就回到主窗口了

    ![Tearof]({{ BASE_PATH }}/assets/images/tools/fiddler/20150429200200.jpg)

17. **q. MSDN search**
    
    这个就不用说了，fiddler的作者在创作fiddler的前几年一直就职于微软。

18. **r. 帮助**
    
    打开帮助页 [fiddler文档](http://docs.telerik.com/fiddler/configure-fiddler/tasks/configurefiddler])

----

###`2:`会话窗口

![会话窗口]({{ BASE_PATH }}/assets/images/tools/fiddler/20150430174338.jpg)
    
- 点击红框标记中的每一项可以升序降序排序会话
- 选中任一项或多项会话后可以再右边3框中选择各种针对当前选中会话的操作在4框中查看和操作详情

####<a id="signMean"></a>符号的意义

![会话窗口]({{ BASE_PATH }}/assets/images/tools/fiddler/20150518155850.jpg)

----


###`3/4:`会话功能区

不限于这些功能可以安装第三方插件

![会话功能区]({{ BASE_PATH }}/assets/images/tools/fiddler/20150430183658.jpg)

1. <a id="Filters">**`a. Filters`**</a>

    过滤器，说事过滤器，就是在每个会话的请求和回复的时候加一些条件或者增减一些数据。如下图：

    ![Filters]({{ BASE_PATH }}/assets/images/tools/fiddler/20150430184020.jpg)

    我们阔以在
    **Request Headers** 中过滤出一些域来增加一些规则， 比如在所有请求域增加一些请求头数据
    
    **Response Headers** 同理，只是其操作的是响应头。
    
    **Breakpoints** 即在所示条件下断点调试
    
    其他的用得着的自己看看


---


2. <a id="Inspectors">**`b. Inspectors`**</a>
    
    如下图显示了会话中的各种信息，包括
    `Header`, `Cookies`, `JSON`, `XML`, `Caching`,

    `TextView`(以纯文本的形式显示上传或响应体), 

    `SyntaxView`(如果ContentType是html,xml,json等会用语法高亮格式化显示), 

    `wevForms`(这个在请求头中提交的表单信息), 

    `HexView`(请求或响应的16进制对应数据),

    `Auth`(授权信息ps:这个我记得基于IIS的ASP.NET里面有开源的库专门做这件事),
    
    `ImageView`(响应数据图片查看器),

    `Raw`(显示原始的响应头).

    上半部分是请求头`Request Headers`, 下半部分是响应头`Response Header`

    ![Inspectors]({{ BASE_PATH }}/assets/images/tools/fiddler/20150430185543.jpg)


---


3. **`c. Statistics`**

    显示一个或多个(选中)会话的性能统计信息,这个在chrome浏览器中有更清晰的统计方式，这里就不多说这个了

    ![Statistics]({{ BASE_PATH }}/assets/images/tools/fiddler/20150504123711.jpg)


---


4. <a id="AutoResponder">**`d. AutoResponder`**</a>

    **自动响应器**：这就是Fiddler可以调试线上代码的一大功能所在。实际它更像一个拦截器，把匹配到的会话连接拦截并使用用户设置的响应体或连接去响应。

    默认情况下这个框是灰色的

    `**->**`选中`Enable automatix responses`打开这个拦截器开始你愉快的调试(测试)之旅

    `**->**`可以点击`Add Rule`添加拦截规则，这里提供了很多种匹配方式，当然也不会少了正则匹配（同时还提供了Test功能，你可以在Test弹窗中测试你写的正则是否能批配到当前回话会其他你指定的URL并保存）。
    可以把匹配到的会话请求转接到本地文件或者其他的URL链接等。还可以把规则通过Import和Export导入导出，为团队开发提供了遍历。

    `**->**`选中`Enable Latency`延迟功能，这不用说大家都知道是在响应的时候做了延迟操作。
    右键点击人你想演示响应的规则会出现下图中的弹窗，除了马上讲的`Set latency` 还有其他许多能在当前规则上的操作，CRUD(增删改查不可少)，添加注释功能与会话框中的注释功能一个意思。

    `Set latency`点击该选项后会出现延时的输入框，这里的单位是ms
    
    其他的功能自己看着办了

    ![AutoResponder]({{ BASE_PATH }}/assets/images/tools/fiddler/20150504132815.jpg)
    
    在我们调试线上页面的时候，我们阔以把线上页面的资源(css/js等)转到本地，用本地的代码去测试线上页面。

---


5. **`e. log`**

    提供了Fiddler操作的过程记录，这个不用讲了，开发和QA都很熟悉的

    ![log]({{ BASE_PATH }}/assets/images/tools/fiddler/20150505201053.jpg)


---


6. <a id="FiddlerScript">**`f. FiddlerScript`**</a>

    这是Fiddler提供给使用者或开发者的一个hook/plugin入口,也是fiddler可扩展的地方。

    在`Go to`中你可以选择定位到各个hook回调的函数。比如在响应体里面加一个CORS响应头。这里给开发者提供了更灵活的扩展Fiddler的可能，go ahead.

    更多请看链接，[FiddlerScript](/categories.html#fiddler-ref)

    ![FiddlerScript]({{ BASE_PATH }}/assets/images/tools/fiddler/20150505201352.jpg)


---


7. <a id="Composer">**`g. Composer`**</a>

    在这里，你阔以在

    **`Parsed选项卡`**中模拟发送如下图列表所示的多种请求，通过点击`Upload file`可以模拟上传文件

     **`Scratchpad选项卡`**中可以存储多个会话的请求头提供分析和修改(只需把需要分析的会话拖到这个框中即可)，当选中某一个请求头的时候还可以使用当前的模拟器发送对应的请求

    ![Composer]({{ BASE_PATH }}/assets/images/tools/fiddler/20150505201708.jpg)


---


8. **`h. Timeline`**

    资源加载的时间进度条这个不讲了，使用chrome中的Timeline就好了

---

###`5`快捷功能

![Composer]({{ BASE_PATH }}/assets/images/tools/fiddler/20150516165153.jpg)

如上图:

a. 第一个是<a id="Capturing">`Capturing`</a>，控制捕获会话的快捷开关;

b. 第二个是选择当前需要捕获的会话的来源程序，所有程序、浏览器、非浏览器、都不捕获选项。

c. <a id="reqResBreakPoint"></a>第三个是会话request发送前的断点和response返回后且到浏览器前的断点，分别对应点一次，点两次该按钮（默认是空白即不设断点），[request断点和response断点详情]({{ BASE_PATH }}/tools/fiddler/2015/05/18/tools-fiddler-function-list/#beforeRequest)中详解

后面的而是当前选中会话的一些标识和描述，如当前选中的是127个会话中的第一个, 详细url为`http://www......`

---

###`6`命令行

快速执行命令行,关于命令的[详情请看]({{ BASE_PATH }}/tools/fiddler/translate/0000/04/11/Translate-fiddler-quick-exec)

![Composer]({{ BASE_PATH }}/assets/images/tools/fiddler/20150516194406.jpg)

