---
layout: post
category : [Touch,Debug]
title: "touch端页面调试"
tagline: "debug"
tags : [touch, 调试, debug, safari, chrome]
excerpt: 调试是定位bug完善程序的最佳实践，移动时代里touch端调试也不能落后。
---
{% include JB/setup %}



####<a id="chrome_debug"></a>Chrome Debug

**Android机**

- 首先你得手机需要安装chrome浏览器>32，你的Android >4
- 打开usb调试
- 打开开发者模式
- 链接usb后选择允许
- 在chrome中打开[chrome://inspect/#devices](chrome://inspect/#devices)

![Alt text]({{ BASE_PATH }}/assets/images/chromium/20150526-1.png)

- 点击`inspect`弹出调试框

![Alt text]({{ BASE_PATH }}/assets/images/chromium/20150627-1.png)

- 可能要对`appspot.com`翻墙（翻墙的话清点此链接[http://honx.in/_VW5jbokWGgfZEPZO](http://honx.in/_VW5jbokWGgfZEPZO),然后我们愉快的各增加十天vip)

- 然后就像在pc上愉快的调试手机上的页面了

####<a id="safari_debug"></a>Safari Debug

**IOS设备**

- 打开mac上的safari浏览器
- 进入偏好设置`->`高级`->`

![Alt text]({{ BASE_PATH }}/assets/images/safari/20150627-1.png)

- 在移动设备上找到设置
- 找到safari应用`->`高级`->`打开Web检测器`
- 移动设备链接mac，在safari/App webview中打开需要调试的页面

![Alt text]({{ BASE_PATH }}/assets/images/safari/20150627-2.png)

- 就是这么简单，愉快的查bug吧

![Alt text]({{ BASE_PATH }}/assets/images/safari/20150627-4.png)

