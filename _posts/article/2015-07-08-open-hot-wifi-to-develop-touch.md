---
layout: post
category : [Tools,WiFi热点]
title : "开启你的热点，为移动开发加油"
tagline: "利其器"
tags : [mac热点, windows热点]
excerpt: 在开发调试移动端的时候我们不能总是外加一个路由器来绑定host,特别在项目较多,设备不足的情况下,我们可能就要用自己的电脑来为手机提供一下网络支持。
---
{% include JB/setup %}
<style>
img{
    width:50%;
}
</style>
###mac

**步骤**

打开设置`->`选择共享`->`如下图

![]({{ BASE_PATH }}/assets/images/tools/hotwifi/20150709-2@2x.png)

先选择共享来源（ps:一般共享有线网到wi-fi）`->`选择用Wi-Fi端口共享给电脑（也就是共享给手机或其他电脑连接）`->`这时你可能需要配置一下网络名和密码（如下图）`->`看到如下界面

![]({{ BASE_PATH }}/assets/images/tools/hotwifi/20150709-1@2x.png)

确认后`->`回到上一幅图，选择打开左边列表中的互联网共享`->`启动`->`

在手机或其他设备上连接你打开的wifi热点：

1.在ios上可以直接使用你的`wifi名.local`访问电脑的80端口。

2.在android上可能要像另一种方法那样（ps：另一种手机访问电脑上的服务的方法是让你的服务所在的电脑与你的手机连上同一个wifi网络，然后在手机上使用电脑的ip访问电脑上的服务。这种方法有局限就是需要在同一网域下连同一个wifi）用电脑的ip来访问电脑上的服务了。


###window

1. 安装360安全卫士
2. 安装wifi共享精灵