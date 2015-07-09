---
layout: post
category : [Style,Touch]
title: "css媒体查询"
tagline: "Media Queries"
tags : [css, media, 响应式布局, 媒体查询]
excerpt: 通过css的media来实现对不同媒介、设备定制不同适配样式的选择，是css实现响应式布局的关键
---
{% include JB/setup %}

###科普
media是css2加入的样式规则，通过media查询可以对不同的尺寸，类别的设备加以不同的样式匹配。
media由两部分组成：media type和media query

**<a href="http://www.w3.org/TR/CSS21/media.html#media-types">media type</a>**:

- all: 匹配所有设备
- braille: 盲文触摸反馈设配
- embossed: 盲文打印机
- handleld: 手持设备，小屏小带宽设备
- print: 打印机样式，打印预览样式
- projection: 投影模式，PPT等
- screen: 主要是彩色电脑屏幕设备，`media queries4`草案中匹配所有没有匹配到print和speech的设备
- speech: 匹配屏幕阅读器页面
- tty: 无像素屏幕匹配的打字机，终端机等
- tv: 电视机类型的设备

###使用方式

**link**

```html
<link rel="stylesheet" media="screen and (max-width:320px)" href="lte320.css" .../>
```

**style**

```html
<style media="screen and ..."></style>
```

**@media**

```css
@media screen and ... {
    body{
        ...
    }
}
```

**@import**

```
@import url("styles/base.css") screen;
```


###`<link rel="stylesheet"/>`
####一、最大宽度`Max Width`

```html
<link rel="stylesheet" type="text/css" media="screen and (max-width:320px)" href="lte320.css" />
```

当屏幕`<=320px`时，将引入lte320.css的样式。

####二、最小宽度`Min Width`

```html
<link rel="stylesheet" type="text/css" media="screen and (min-width:900px)" href="gte.css" />
```

当屏幕`>=900px`时，将引入gte.css的样式。

####三、多个Media Queries使用

```html
<link rel="stylesheet" type="text/css" media="screen and (min-width:600px) and (max-width:900px)" href="style.css" />
```

Media Query可以结合多个媒体查询，换句话说，一个Media Query可以包含0到多个表达式，表达式又可以包含0到多个关键字，以及一种Media Type。正如上面表示的是当屏幕在600px-900px之间时采用style.css样式来渲染web页面。

####四、设备屏幕的输出宽度Device Width

```html
<link rel="stylesheet" type="text/css" media="screen and (max-device-width: 480px)" href="iphone.css" />
```

上面的代码指的是iphone.css样式适用于最大设备宽度为480px，比如说iPhone上的显示，这里的max-device-width所指的是设备的实际分辨率，也就是指可视面积分辨率

####五、iPhone4

```html
<link rel="stylesheet" type="text/css" media="only screen and (-webkit-min-device-pixel-ratio: 2)" href="iphone4.css" />
```

上面的样式是专门针对iPhone4的移动设备写的。

####六、iPad 屏幕纵向(orientation:portrait)，横向(orientation:landscape)

```html
<link rel="stylesheet" type="text/css" media="all and (orientation:portrait)" href="portrait.css" /><link rel="stylesheet" type="text/css" media="all and (orientation:landscape)" href="landscape.css" />
```

在大数情况下，移动设备iPad上的Safari和在iPhone上的是相同的，只是他们不同之处是iPad声明了不同的方向，比如说上面的例子，在纵向portrait时采用portrait.css来渲染页面；在横向landscape时采用landscape.css来渲染页面。

####七、android

/*240px的宽度*/

```html
<link rel="stylesheet" type="text/css" media="only screen and (max-device-width:240px)" href="android240.css" />
```

/*360px的宽度*/

```html
<link rel="stylesheet" type="text/css" media="only screen and (min-device-width:241px) and (max-device-width:360px)" href="android360.css />
```

/*480px的宽度*/

```html
<link rel="stylesheet" type="text/css" media="only screen and (min-device-width:361px) and (max-device-width:480px)" href="android480.css" />
```

我们可以使用media query为android手机在不同分辨率提供特定样式，这样就可以解决屏幕分辨率的不同给android手机的页面重构问题。

####八、not关键字

```html
<link rel="stylesheet" type="text/css" media="not print and (max-width: 1200px)" href="print.css" />
```

not关键字是用来排除某种制定的媒体类型，换句话来说就是用于排除符合表达式的设备。

####九、only关键字

```html
<link rel="stylesheet" type="text/css" media="only screen and (max-device-width:240px)" href="android240.css" />
```

only用来定某种特定的媒体类型，可以用来排除不支持媒体查询的浏览器。其实only很多时候是用来对那些不支持Media Query但却支持Media Type的设备隐藏样式表的。其主要有：支持媒体特性（Media Queries）的设备，正常调用样式，此时就当only不存在；对于不支持媒体特性(Media Queries)但又支持媒体类型(Media Type)的设备，这样就不会读样式了，因为其先读only而不是screen；另外不支持Media Qqueries的浏览器，不论是否支持only，样式都不会被采用。

####十、注意

在Media Query中如果没有明确指定Media Type，那么其默认all，如：

```html
<link rel="stylesheet" type="text/css" media="(min-width: 701px) and (max-width: 900px)" href="medium.css" />
```

另外还有使用逗号（，）被用来表示并列或者表示或，如下

```html
<link rel="stylesheet" type="text/css" href="style.css" media="handheld and (max-width:480px), screen and (min-width:960px)" />
```

上面代码中style.css样式被用在宽度小于或等于480px的手持设备上，或者被用于屏幕宽度大于或等于960px的设备上。



###CSS文件中@media引入使用max-width

```css
@media screen and (max-width: 600px) {
/* CSS Styles */
}
```

####一、使用min-width

```css
@media screen and (min-width: 900px) {
/* CSS Styles */
}
```

####二、max-width和min-width的混合使用

```css
@media screen and (min-width: 600px) and (max-width: 900px) {
/* CSS Styles */
}
```

####三、同时CSS3 Media Queries还能查询设备的宽度device-width来判断样式的调用，这个主要用在iPhone,iPads设备上，比如像下面的应用：

**iPhone和Smartphones上的运用**

```
/* iPhone and Smartphones (portrait and landscape) */@media screen and (min-device-width : 320px) and (max-device-width: 480px) {
/* CSS Styles */
}
```

四、max-device-width指的是设备整个渲染区宽度（设备的实际宽度），而 max-width 指的是可视区域分辨率。

**iPad上的运用**

```
/* iPads (landscape) */@media screen and (min-device-width : 768px) and (max-device-width : 1024px) and (orientation : landscape) {
/* CSS Styles */
}
/* iPads (portrait) */@media screen and (min-device-width : 768px) and (max-device-width : 1024px) and (orientation : portrait) {
/* CSS Styles */
}
```

针对移动设备的运用，如果你想让样式工作得比较正常，需要在“”添加viewport的meta标签

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

上面针对不同的分辨率，不同的移动设备类型做了不同定义，可能我们在实际的使用中用不着这么详细的去区分不同的设备。

比如针对电脑访问的就制定一个大于800到1280，超过1280的，就显示一个大分类的样式





