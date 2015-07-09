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

####语法

```js
media_query_list: <media_query> [, <media_query> ]*
media_query: [[only | not]? <media_type> [ and <expression> ]*]
  | <expression> [ and <expression> ]*
expression: ( <media_feature> [: <value>]? )
media_type: all | aural | braille | handheld | print |
  projection | screen | tty | tv | embossed
media_feature: width | min-width | max-width
  | height | min-height | max-height
  | device-width | min-device-width | max-device-width
  | device-height | min-device-height | max-device-height
  | aspect-ratio | min-aspect-ratio | max-aspect-ratio
  | device-aspect-ratio | min-device-aspect-ratio | max-device-aspect-ratio
  | color | min-color | max-color
  | color-index | min-color-index | max-color-index
  | monochrome | min-monochrome | max-monochrome
  | resolution | min-resolution | max-resolution
  | scan | grid
```

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

如果没有明确指定Media Type，那么其默认all

```html
<link rel="stylesheet" type="text/css" media="(min-width: 600px) and (max-width: 900px)" href="medium.css" />
```

**<a href="http://www.w3.org/TR/CSS21/media.html#media-types">media query属性</a>**:

media query是media type的扩展

使用逗号`,`表示或，`and`表示与，`only`表示唯一性（只满足），`not`表示非，可以混合使用，如下面的例子（可能很诡异，只是个例子）

```css
@media (only screen and (min-width:960px)), handheld and (max-width:480px), (not tv) and (color) {
    /*匹配查询条件
    只有是屏幕的时候宽度大于等于960px的
    或手持设备并且宽度小于等于480px的
    或不是tv的设备是有色的
    的样式*/
}
```

`only`: 用来指定某种特定的媒体类型，可以用来排除不支持媒体查询的浏览器。

only很多时候是用来对那些不支持Media Query但却支持Media Type的设备隐藏样式表的。

其主要有：

- 支持媒体特性（Media Queries）的设备，正常调用样式，此时就当only不存在；

- 对于支持媒体类型(Media Type)而不支持媒体特性(Media Queries)的设备，only可以只判断当前media type是否为真而是都采用后续样式；

- 对于不支持Media Qqueries的浏览器，不论是否支持only，样式都不会被采用。

---

大多数媒体属性带有“min-”和“max-”前缀，用于表达“大于等于”和“小于等于”。如果未指定属性的值，并且该属性的实际值不为零，则该表达式被解析为真。

**宽高比（aspect-ratio）**

>
值：`<ratio>比例`
媒体： `visual, tactile`
min/max 前缀：`是`

描述了输出设备目标显示区域的宽高比。该值包含两个以“/”分隔的正整数。代表了水平像素数（第一个值）与垂直像素数（第二个值）的比例。

*示例*

下面为显示区域宽高至少为一比一的设备选择了一个特殊的样式表。`数学分数比较`

```css
@media screen and (min-aspect-ratio: 1/1) { }
```

这指定了宽高比或者1：1或者更大。换句话说，可视区域或者是正方形或者是宽屏。

**颜色（color）**

>
值： `<color>`
媒体： `visual`
min/max 前缀：`是`

指定输出设备每个像素单元的比特值。如果设备不支持输出颜色，则该值为0。

注意：如果每个颜色单元具有不同数量的比特值，则使用最小的。例如，如果显示器为蓝色和红色提供5比特，而为绿色提供6比特，则认为每个颜色单元有5比特。如果设备使用索引颜色，则使用颜色表中颜色单元的最小比特数。

*示例*

向所有能显示颜色的设备应用样式表：

```css
@media all and (color) { }
```

向每个颜色单元至少有4个比特的设备应用样式表：`rgba(255,255,255,255)`

```css
@media all and (min-color: 4) { }
```

**颜色索引（color-index）**

>
值：`<integer>`
媒体：` visual`
min/max 前缀：`是`

指定了输出设备中颜色查询表中的条目数量。

*示例*

向所有使用索引颜色的设备应用样式表，你可以这么做：

```css
@media all and (color-index) { }
```

向所有使用至少256个索引颜色的设备应用样式表：`?解释索引颜色`

```html
<link rel="stylesheet" media="all and (min-color-index: 256)" href="http://foo.bar.com/stylesheet.css" />
```

**设备宽高比（device-aspect-ratio）**

>
值：`<ratio>`
媒体：`visual, tactile`
min/max 前缀：`是`

描述了输出设备的宽高比。值与`aspect-ratio`相同。

*示例*

下面为宽屏设备选择了一个特殊的样式表。

```
@media screen and (device-aspect-ratio: 16/9), screen and (device-aspect-ratio: 16/10) { }
```

宽高比或者16：9或者16：10。

**设备高度(device-width) **

>
值：`<length>`
媒体：`visual, tactile`
min/max 前缀：`是`

描述了输出设备的高度（整个屏幕或页的高度，而不是仅仅像文档窗口一样的渲染区域）。

*示例*

向显示在最大宽度800px的屏幕上的文档应用样式表，你可以这样做：

```html
<link rel="stylesheet" media="screen and (max-device-width: 799px)" />
```

**设备宽度(device-height) **

>
值：`<length>`
媒体： `visual, tactile`
min/max 前缀：`是`

描述了输出设备的宽度（整个屏幕或页的高度，而不是仅仅像文档窗口一样的渲染区域）。

```html
<link rel="stylesheet" media="screen and (min-device-height: 799px)" />
```

**网格（grid）**

>
值：`<integer>`
媒体：`all`
min/max 前缀： `否`

判断输出设备是网格设备还是位图设备。如果设备是基于网格的（例如电传打字机终端或只能显示一种字形的电话），该值为1，否则为0。

*示例*

向一个15字符宽度或更窄的手持设备应用样式：

```css
@media handheld and (grid) and (max-width: 15em) { }
```

注意：`em` 在网格设备中有不同的意义；一个`em`的实际宽度不得而知，假设1em相当于一个网格单元的宽高。
`?css中的是当前样式规则中的字体的大小`

**高度（height）**

>
值：`<length>`
媒体：`visual, tactile`
min/max 前缀：`是`

height 媒体属性描述了输出设备渲染区域（如可视区域的高度或打印机纸盒的高度）的高度。

```css
@media handheld and (grid) and (min-height: 15em) { }
```


**黑白（monochrome）**

>
值：`<integer>`
媒体： `visual`
min/max 前缀：`是`

指定了一个黑白（灰度）设备每个像素的比特数。如果不是黑白设备，值为0。

*示例*

向所有黑白设备应用样式表：

```css
@media all and (monochrome) { }
```

向每个像素至少8比特的黑白设备应用样式表：

```css
@media all and (min-monochrome: 8) { }
```

**方向（orientation）**

>
值：`landscape 横屏 | portrait 竖屏`
媒体：`visual`
min/max 前缀：`否`

指定了设备处于横屏（宽度大于宽度）模式还是竖屏（高度大于宽度）模式。

*示例*

向竖屏设备应用样式表：

```css
@media all and (orientation: portrait) { }
```

**分辨率（resolution）**

>
值： `<resolution>`
媒体： `bitmap`
min/max 前缀：`是`

指定输出设备的分辨率（像素密度`dppx`）。分辨率可以用每英寸`dpi`或每厘米`dpcm`的点数来表示。

*示例*

为每英寸至多300点的打印机应用样式：

```css
@media print and (min-resolution: 300dpi) { }
```

替换老旧的 (min-device-pixel-ratio: 2) 语法：

```css
@media screen and (min-resolution: 2dppx) { }
```

**扫描（scan）**

>
值： `progressive | interlace`
媒体：`tv`
min/max 前缀：`否`

描述了电视输出设备的扫描过程。

*示例*

向以顺序方式扫描的电视机上应用样式表：

```css
@media tv and (scan: progressive) { }
```

**宽度（width）**

>
值： `<length>`
媒体： `visual, tactile`
min/max 前缀：`是`

width 媒体属性描述了输出设备渲染区域（如可视区域的宽度或打印机纸盒的宽度）`viewport`的宽度。

如果你想向最小宽度20em的手持设备或屏幕应用样式表，你可以使用这样的查询：

```css
@media handheld and (min-width: 20em), screen and (min-width: 20em) { }
``` 

这个媒体查询将向最小宽度8.5英寸的打印机应用样式表：

```html
<link rel="stylesheet" media="print and (min-width: 8.5in)"
    href="http://foo.com/mystyle.css" />
```


这个查询适用于宽度在500px和800px之间的屏幕：

```css
@media screen and (min-width: 500px) and (max-width: 800px) { }
```



`引:`以上media属性内容截自https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries

---



###使用方式

**link**

```html
<link rel="stylesheet" media="screen and (max-width:320px)" href="lte320.css" .../>
```

**style**

```html
<style media="screen and ..."></style>
```

**css文件中 @media**

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
####iPhone4

```html
<link rel="stylesheet" type="text/css" media="only screen and (-webkit-min-device-pixel-ratio: 2)" href="iphone4.css" />
```

上面的样式是专门针对iPhone4的移动设备写的。

####iPad 屏幕纵向(orientation:portrait)，横向(orientation:landscape)

ipad与iphone不同之处是`iPad声明了不同的方向`，可如下处理

```html
<link rel="stylesheet" type="text/css" media="all and (orientation:portrait)" href="portrait.css" /><link rel="stylesheet" type="text/css" media="all and (orientation:landscape)" href="landscape.css" />
```



####CSS3 Media Queries还能查询设备的宽度device-width来判断样式的调用，这个主要用在iPhone,iPads设备上，比如像下面的应用：

**iPhone和Smartphones上的运用**

```
/* iPhone and Smartphones (portrait and landscape) */@media screen and (min-device-width : 320px) and (max-device-width: 480px) {
/* CSS Styles */
}
```

####移动touch

max-device-width指的是设备整个渲染区宽度（设备屏幕实际可渲染宽度），而 max-width 指的是viewport可视区域分辨率。

针对移动设备需要在head中添加viewport的meta。指定宽度为设备宽度，否则将像PC端那样按实际像素渲染。

```html
<meta content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no" name="viewport">
```