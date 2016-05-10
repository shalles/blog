---
layout: post
category : [Tools, Browser, Chromium, RT]
title : "浏览器的一些基本知识(RT)"
tagline: "利其器"
tags : [浏览器, browser, Chromium, Chrome, webkit, blink]
excerpt: 对前端开发有帮助的一些浏览器的基础知识。
---
{% include JB/setup %}

<!-- img: { url: "/assets/images/tools/fiddler/20150429140745.jpg", alt: "浏览器的一些基本知识(RT)"} -->
今年3-5月误入歧途，学习起webkit和chromium的知识来。给大家推荐一本书《webkit技术内幕》。
两三个月的时间仔细看了两遍。 第一遍一字不漏的精读了一遍，留下了很多疑问，但很快后面的章节给出了一些答案。第二遍 带着剩下的问题在看了一变，并对照里面讲到的与实践对照了一番。chromium代码更新较快，但短时间总体架构不会有太大变化，再说我不是为了看代码儿看，我的目的很简单，就是在他的设计中找到一些实现原理，以便在前端开发中提供一些优化依据等。
然后是直接到官网看文档。然后一拖就到了现在。因为很虚，感觉总结写点东西但网上都能查到，但还是希望能帮到有需要的人，没有那就留给自己也是极好的。

## 万事先统一 来点概念备忘：

### 用户代理（User Agent）

这个代理不是网络代理，而是给服务端识别用户浏览器身份的标识，相当于浏览器的一个别名。这个别名在不同浏览器之间有不同的标识，相同的浏览器在不同的设备下也有不同的标识。服务端识别浏览器的身份知道发起请求的浏览器能支持怎样的功能而返回不同的数据，这在移动时代更加显现。比如在访问一些网站的主域名的时候，你在不同的浏览器或者不同的设备上会发现浏览器浏览到的网页被重定向到了不同的子域名地底下。这里以[http://qunar.com](http://qunar.com)为例，在不同的设备下的你会被重定向到不同的子域底下，如在pad上你会被重定向到http://pad.qunar.com, 在手机上你会别重定向到http://touch.qunar.com。

这时你可能会想了，各家浏览器出来的时间都不近相同，那也没看到服务端每次去更新自己的支持以识别各家新浏览器。
也有可能会疑惑一个浏览器的UA怎么回那么长，不仅写着自己的标识，还写着其他浏览器的标识。
我们来看一下chrome的用户代理标识在浏览器重输入`chrome://version`

```js
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/42.0.2311.152 Safari/537.36
```

或者可能你通过其他方式改变了你的UA，因此可以在console中输入navigator.useragent查看当前的UA标识。但这不重要，我们看，Chrome的UserAgent中除了自家浏览器的标识还有Mozilla、AppleWebKit、Safari其他浏览器的标识。因为让网页提供商即已有的诸多服务端去增加对新浏览器的识别不大现实，且新浏览器的功能也能满足他UA中的其他浏览器能实现的功能，所以浏览器加入了其能支持的其他浏览器的UA标识，让服务商也为他提供数据。当然这个跟浏览器出现的早晚也没有绝对的关系，比如随着Chrome市场占有率的逐渐增加，各个服务商更加愿意为带有Chrome标识的浏览器提供更多数据。

### 一般渲染引擎的主要模块及渲染过程

1. HTML解释器 产物：DOM树

2. css解析器

3. javascript引擎

4. layout布局

5. 绘图

#### 一般渲染过程

首先网页内容加载到浏览器后，进入HTML解析器，边解析边构建一棵DOM树，在此构建过程中遇到内嵌的css则交给css解析器去解析并存储为内部规则,HTML解析器不暂停，遇到外联js文件则等待问价加载，内嵌js则直接交给javascript引擎处理，且HTML解析器暂停，在内嵌js解析过程中可能操作修改已插入DOM树的节点或创建新节点等。遇到外部资源文件交给资源加载器加载，一般除无明显标记异步加载的js文件外不会暂停HTML的解析。在建立DOM树后渲染引擎使用接收css解析器解析的样式信息进行规则匹配和布局计算并把这些数据保存到ReaderObject对象，用于之后的渲染并构建渲染对象树(渲染层树 软硬件渲染 合并)。构建完渲染树后就开始绘制了。(后面详细分析，包括重绘，draw和print ...)

关于draw和print可以看另外一篇[在Chrome中的GPU加速合成]({{ BASE_PATH }}/Browser/Chromium/ RT/2015/09/20/GPU-Accelerated-Compositing)

### 包含块模型

根元素的包含块称为出事包含块， 通常大小就是可以区域
position：static/relative的元素，包含块是器最近祖先的盒模型的内容区域
position：fixed的元素，其包含块脱离HTML文档，固定咋可视区域
position：absolate的元素的包含块有其最近定位祖先元素决定
inline的元素的包含块是其祖先元素的第一个inline元素和最后一个inline元素框的内边距的区域

#### 影子DOM

它允许在文档（document）渲染时插入一棵DOM元素子树，但是这棵子树不在主DOM树中

```js
if (Element.prototype.createShadowRoot){
    shadowRoot = componentEle.createShadowRoot();
    shadowRoot.appendChild(statusBarEle);
    shadowRoot.appendChild(headerEle);
} else {
    componentEle.appendChild(statusBarEle);
    componentEle.appendChild(headerEle);
}
```

[**Demo**](https://github.com/shalles/FET-chrome-extention/blob/master/simulate-platform-header/src/js/content.js)

#### css规则匹配为什么从右到左