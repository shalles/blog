---
layout: post
category : Tools
title : "开发中模拟网速慢的方法"
tagline: "利其器"
tags : [comment, coding skill, code]
excerpt: 开发中可能我们需要模拟一些网速慢的场景，比如ajax等受网速影响的异步操作，在开发测试中经验不足很容易出错
---
{% include JB/setup %}

###第一个利器 chromium中的调试器中的Emulation设备仿真工具

下面以chrome浏览器为例（这对于无线移动端开发的同学已经不是什么新鲜事了）

**打开方式**： 打开chrome浏览器，（`F12`/`Ctrl+Shift+i`）打开开发者工具 点击`Show drawer`按钮，如下图`>三`，然后出现如下图所以的新层

![chrome-Emulation]({{ BASE_PATH }}/assets/images/tools/chromium/20150515115650.jpg)

然后选择`Emulation`：
这时你可能会看到一句话 
`Emulation is currently disabled Toggle  in the main toolbar to enable it`

这就是上图中左上角红框中的移动设备图标没有选中，选上就行了。
这时对于PC端的同学不要担心你的浏览页面变样了，选择自己想要的设备或或拖动自己需要的尺寸

再选择netWork选项，然后就可以在`Limit network throughtput`中选择你需要模拟的场景，然后就阔以愉快的模拟各种网速场景了

![chrome-Emulation]({{ BASE_PATH }}/assets/images/tools/chromium/20150515120607.jpg)

###第二个神器Fiddler中的 Simulate Modem Speeds

**打开方式**： 如下图在Fiddler中打开性能模拟猫速选项（关于Fiddler的教程很快就会跟进）

![模拟猫的网速]({{ BASE_PATH }}/assets/images/tools/fiddler/20150515122102.jpg)

###第三个利器 Network Delay Simulator

这个没有fiddler方便，网站可能需要翻墙
http://www.verboon.info/2010/06/tooltip-network-delay-simulator/

![Network Delay Simulator]({{ BASE_PATH }}/assets/images/tools/NetworkDelaySimulator/20150515160729.jpg)

###如果使用的是虚拟机

这里以VMware虚拟机工具为例 
`编辑虚拟机设置` -> `网络适配器` -> `高级` 如下图

除了配置带宽，选择不同情境下的网速，在下面还可以配置丢包率。

![虚拟机模拟网速慢]({{ BASE_PATH }}/assets/images/tools/VMware/20150515163152.jpg)

###其他的方式

1.如果你按装了一些安全卫士这样的软件，上面一般都会有网速限制的功能;

2.自己的路由可以登录路由器在上面限制测试机的网速;

3.评论你面