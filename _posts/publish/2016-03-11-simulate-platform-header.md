---
layout: post
category : [Tools, Publish]
title : "PC模拟微信,支付宝,QQ等内嵌网页header"
tagline: "利其器"
tags : [模拟, 微信, 支付宝, QQ, header, 样式]
excerpt:  开发微信，支付宝，QQ，端等hybrid应用时，我们会用到这些平台自己的header。但在PC上开发调试时没有这样的头部，有事会发现UI的设计也没有吧这部分尺寸考虑进去，导致计算上和显示视觉上的偏差。
---
{% include JB/setup %}

<style>
	img {
		width: auto;
		max-width:100%;
	}
</style>

开发微信，支付宝，QQ，端等hybrid应用时，我们会用到这些平台自己的header。但在PC上开发调试时没有这样的头部，有事会发现UI的设计也没有吧这部分尺寸考虑进去，导致计算上和显示视觉上的偏差。因此为了开发调试便利，我们可以在PC上模拟这些应用的头部包括显示时间电量的状态栏。

说干就干。首先写好了一份header的样式没的说，那我们每做一个项目都要在开发的时候在每个页面里面引入这部分代码，然后上线的时候在删除引用吗？

为了方便我们可以写一个chrome的插件，如果不是chrome的话可能真的得每个页面都得引入写header的代码。

### 安装

[下载simulate-platform-header.crx文件](http://shalles.github.io/assets/extention/simulate-platform-header.crx)

打开chrome浏览器的扩展页, 将simulate-platform-header.crx文件拖动到该页上, 点击add extention

<img style="max-width: 100%" src="http://shalles.github.io/assets/images/article/2.pic.jpg" alt="安装simulate-platform-header.crx">

### 使用

然后打开移动开发模式并选择需要模拟的平台header

<img style="width: 100px" src="http://shalles.github.io/assets/images/article/3.pic.jpg" alt="开始使用">

<img style="width: 100%; max-width: 320px;" src="http://shalles.github.io/assets/images/article/1.pic.jpg" alt="demo">

### 如果有其他需要自己改代码安装

[代码链接](https://github.com/shalles/FET-chrome-extention/tree/master/simulate-platform-header)