---
layout: post
category : ouch
title: "touch开发备忘录（二）"
tagline: "summary"
tags : [css, meta, 网页关键字]
excerpt: 写惯了PC前端，移动端开发可能会然你更爽，html5/css3的新特性都可以用了，终于可以摆脱IE了。但新的问题又来了，各手机厂商定制了自己的系统，各浏览器渲染引擎的不同实现或不同配置。移动端也许没有PC那么简单。但问题记录下来并找到解决方案，就不在可怕了
---
{% include JB/setup %}

###Code



1、移动端如何定义字体font-family

三大手机系统的字体：

ios 系统

默认中文字体是Heiti SC
默认英文字体是Helvetica
默认数字字体是HelveticaNeue
无微软雅黑字体

android 系统

默认中文字体是Droidsansfallback
默认英文和数字字体是Droid Sans
无微软雅黑字体

```css
body{
    font-family:Helvetica;
}
```

2、移动端字体单位font-size选择px还是rem

对于只需要适配手机设备，使用px即可

对于需要适配各种移动设备，使用rem，例如只需要适配iPhone和iPad等分辨率差别比较挺大的设备

rem配置参考：

```css
html {font-size:10px}
@media screen and (min-width:480px) and (max-width:639px) {
    html {
        font-size: 15px
    }
}
@media screen and (min-width:640px) and (max-width:719px) {
    html {
        font-size: 20px
    }
}
@media screen and (min-width:720px) and (max-width:749px) {
    html {
        font-size: 22.5px
    }
}
@media screen and (min-width:750px) and (max-width:799px) {
    html {
        font-size: 23.5px
    }
}
@media screen and (min-width:800px) and (max-width:959px) {
    html {
        font-size: 25px
    }
}
@media screen and (min-width:960px) and (max-width:1079px) {
    html {
        font-size: 30px
    }
}
@media screen and (min-width:1080px) {
    html {
        font-size: 32px
    }
}
```

```

```