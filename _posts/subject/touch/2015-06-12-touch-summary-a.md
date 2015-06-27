---
layout: post
category : ouch
title: "touch开发备忘录"
tagline: "summary"
tags : [css, media, 响应式布局, 媒体查询]
excerpt: 移动时代的到来，
---
{% include JB/setup %}

###基础
####摘要

- 一台阔以配置host的路由器（eg. 极路由：装host插件）
- 安装chrome(android)/safari(ios) [chrome调试](#chrome_debug)，[safari调试](#safari_debug)

####<a id="os"></a>准备

**`系统`**

- ios 6-9
- android 4.1-4.4/5(低版本再考虑)
- wp(可以考虑)

**`浏览器`**

- android/ios自带浏览器
- Android UC QQ 百度浏览器
- Android/ios Chrome  微信/App WebView

**`设备(常见问题设备)`**

- 华为荣耀系列
- 小米 3
- iPhone4(ios 6)
- 三星S4/5 Galaxy 2/3

**`屏幕分辨率`**

- **Android** `480*540` `480*854`  `720*1280` `540*960` `1080*1920`
- **iphone** `640*1136 5+` `640*960 4+`
- **ipad** `1536*2048` `768*1024`

**`网络`**

保持手机连接的网络与服务端相同，即手机能访问到你启动测试电脑上应用的服务。很多奇葩问题出自于网络，开发测试一定要检查网络。

**`缓存`**

记得清缓存，应用缓存/浏览器缓存。一些时候或手机能成功，一些时候失败，那清清缓存吧！

---

###大部分问题阔以通过下面的模拟调试找到答案
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


####代码兼容（这一块未完待续）

**盒模型:**

盒模型尽量使用`box-sizing: border-box`,为什么呢？我们的屏幕总是固定的，浏览器产生横向的滚动条操作是很晃动的。为了便于布局使用border-box(padding和border被包含在定义的width和height之内。对象的实际宽度就等于设置的width值，即使定义有border和padding也不会改变对象的实际宽度,即表现为怪异模式下的盒模型)，我们设定好元素的宽或高后`padding`和`border`就不会把元素撑开而产生非预期的左右滚动等。

```scss
{
   box-sizing: border-box;
   //盒模型改变后我们行内元素height line-height垂直居中的方法就不生效了，但有些手机上是阔以居中的
   height: $h;
   line-height: 1; // $h;
}
```

**布局:**

- 与PC为避开<IE7相反，避免使用float 阔以使用display: line-block或者flex

- 给input指定对应类型，这样可以唤起输入法的对应键盘，如`input[type=number]`可以在焦点的时候唤起九宫格输入面板

- 尽情使用语义化标签
