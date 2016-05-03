---
layout: post
category : [Browser, Chromium, RT]
title : "在Chrome中的GPU加速合成(一)"
tagline: "利其器，伪翻译"
tags : [浏览器, browser, Chromium, Chrome, webkit, blink]
excerpt: 在Chrome中的GPU加速合成,这份文件提供了背景和细节上的Chrome浏览器硬件加速合成的实现。
---
{% include JB/setup %}


Tom Wiltzius, Vangelis Kokkevis & the Chrome Graphics team
updated May 2014

## 摘要

这份文件提供了背景和细节上的Chrome浏览器硬件加速合成的实现。

#### 简介：为什么需要硬件合成？

传统上，网络浏览器完全依靠在CPU上渲染网页内容。以GPU的能力看，现在就算是最小的设备它也是不可或缺的一部分，大家的注意力已经转向于寻找如何更有效地使用这一底层硬件来达到更好的性能和降低功耗。使用GPU合成网页的内容可以得到非常显著速度提升。

#### 硬件合成的好处有三种类型：

1.在绘图和合成操作涉及大量像素的时候，GPU合成页面层可以实现比CPU好得多的效率（包括加速和绘制能力）。GPU就是对这些类型的工作负载而设计的。<br> 
2.CPU不再需要昂贵回读内容的操作，因为他们已经可以在GPU上执行（如加速视频，Canvas2D，或WebGL的）。<br>
3.CPU和GPU，从而可以在同一时间，以建立一个并行有效的图形流水线操作。

## 第1部分：Blink渲染基础

Blink渲染引擎的源代码量是巨大的，复杂的，有些几乎没有记载。为了了解GPU在Chrome浏览器中的加速工作，我们首先去了解Blink如何渲染网页的基本构建模块是非常重要的。

#### 节点和DOM树

在Blink中，一个网页的内容在内部存储在一棵节点对象组成的树称为DOM树。在页面里面的每一个HTML元素都会像在节点建的Text（文本内容）一样，被分配一个节点与之关联。DOM树的顶级节点始终是一个Document节点。

#### 从节点Nodes到渲染对象RenderObjects

在产生视觉输出DOM树的每个节点都有一个相应的渲染对象。RenderObjects 被存储在并行树结构，称为渲染树。一个渲染对象知道如何绘制该节点中的内容上的显示。它通过发出必要的绘制调用到GraphicsContext。一个GraphicsContext负责把像素写入到位图bitmap上, 以便最终显示到屏幕上。在Chrome浏览器中，GraphicsContext包装了的2D绘图库Skia。

典型的大部分GraphicsContext调用转换成了调用SkCanvas或SkPlatformCanvas，即马上绘制(print)到软件的位图上 (Chrome如何使用Skia的更多细节 [见该文件](http://www.chromium.org/developers/design-documents/graphics-and-skia))。然而printing操作将从主线程移除（本文档后面有更详细的版本），替代性的，这些命令现在记录到了[SkPicture](https://code.google.com/p/skia/source/browse/trunk/include/core/SkPicture.h)。SkPicture是可序列化的数据结构，它类似于一个显示列表，可以截获并在之后重新执行命令。

#### 从RenderObjects到RenderLayers

每个渲染对象都直接或间接的通过一个祖先渲染对象与一个渲染层RenderLayer关联。

RenderObjects共享同一个坐标空间（例如受同一个CSS变换影响）通常属于同一RenderLayer。RenderLayers的存在使得页面的元素以正确的顺序进行合成，进而正确地显示重叠内容，半透明元件等或其他一些条件会触发创建新RenderLayer来用于特定渲染对象，就像[RenderBoxModelObject::requiresLayer()](https://code.google.com/p/chromium/codesearch#chromium/src/third_party/WebKit/Source/core/rendering/RenderBoxModelObject.h)中定义和重写的一些派生类。渲染对象一定会建立RenderLayer的常见情况：

1.它是用于页根对象 <br> 
2.它有明确的CSS position属性（相对，绝对或变换）<br>
3.它是透明<br>
4.有溢出，阿尔法口罩或反射<br>
5.有一个CSS滤镜<br>
6.对应于`<canvas>`元素具有3D（WebGL的）上下文或加速的2D背景<br>
7.对应一个`<video>`元素

注意，RenderObjects和RenderLayers之间并不是一对一的对应关系。一个特别的渲染对象会给他分配一个为它创建的RenderLayer，或者使用第一个具有RenderLayer的祖先RenderObject的RenderLayer。

RenderLayers很好的形成树状层次结构。其根节点是RenderLayer对应页面中的根元素及视觉包含在该父层的后代。每个RenderLayer被存储在两个按升序排列的存储列表，negZOrderList 包含具有负z-index的子层（因此图层会显示到当前层以下），posZOrderList 包含具有正z-index的孩子层（图层现实到当前层以上）。

#### 从RenderLayers到GraphicsLayers

要使用合成器，一些（但不是全部）RenderLayers的有自己的后端存储（或背面）（一个渲染层有自己的后端存储（背面）被广泛称为合成层）。 每个RenderLayer要么有自己的GraphicsLayer（如果它是一个合成层），或者使用具有它的一个祖先的GraphicsLayer。这类似于RenderObject渲染对象同RenderLayers关系。

每个GraphicsLayer有GraphicsContext的相关RenderLayers画成。在随后的合成中， 该合成器是负责最终GraphicsContexts整个的位图输出，并组合成一个最终的屏幕图像。

虽然在理论上的每一个RenderLayer可以paint自己到一个后端存储，在实践中，这可能是相当浪费在内存的（尤其是VRAM）。在当前Blink的实现中，在以下条件中必须满足一个的RenderLayer可以获得其自己的合成层（参照[CompositingReasons.h](https://code.google.com/p/chromium/codesearch#search/&q=file:CompositingReasons.h)）：

1.层具有3D或者透视变换的CSS属性<br> 
2.层由`<video>`使用元素加速视频解码<br>
3.层由一个`<canvas>`元素与3D环境或加速的2D背景<br>
4.层用于已合成的插件<br>
5.层使用CSS动画的不透明性，或使用一个动画webkit的变换<br>
6.层使用加速CSS滤镜 opacity filters<br>
7.层的后代是一个合成层<br>
8.层具有较低的z-index的合成层的兄弟（换言之，层重叠上在合成层上，应在它的上面被渲染）

#### 层的挤压

决无例外的规则。如上所述，GraphicsLayers可以在存储器和其它资源方面消耗昂贵的代价（例如，有一些重要的操作的CPU时间复杂度正比于GraphicsLayer树的大小）。许多附加的层可以为RenderLayers创建一个重叠的Re​​nderLayer附和在它的背面，这可能是昂贵的产生。

我们把内在的合成原因“直接”合成原因（例如，有一个3D转换就可以是一层）。为了防止“图层爆炸”时，当很多元素都以一个直接合成的因素在一个层的顶端时，Blink会使多个RenderLayers覆盖到一个有直接合成因素的RenderLayer上，并把他们压到一个后端存储中。这样就可防止因重叠引发层爆炸。更多详细促使层发成挤压的内容可以在此[演示文稿](https://docs.google.com/presentation/d/1WOhbWLkhMyo4vZUaHq-FO-mt0B2sejXw-lMwohD5iUo/edit#slide=id.g2a8a2080a_088)找到，RenderLayers和合成层之间的代码的更细节中可以在[此演示文稿](https://docs.google.com/a/chromium.org/presentation/d/1dDE5u76ZBIKmsqkWi2apx3BqV8HOcNf4xxBdyNywZR8/edit#slide=id.p)找到.

#### 从GraphicsLayers到WebLaye​​rs再到CC层

在讲Chrome的合成器的实现之前我们先来了解两个抽象层！GraphicsLayers可以经由一个或多个Web*Layers表示其内容。这些都是WebKit端需要实现的接口; 看Blink接口的[public/platform](https://code.google.com/p/chromium/codesearch#chromium/src/third_party/WebKit/public/platform/)目录，如：WebContentsLayer.h或WebScrollbarLayer.h文件。Chrome的实现是使用Chrome合成器层的类型来实现抽象类Web*Layer的接口[src/webkit/renderer/compositor_bindings ](https://code.google.com/p/chromium/codesearch#chromium/src/webkit/renderer/compositor_bindings/)它们实现使用Chrome合成器层类型抽象的Web *层接口。

#### 组合在一起：合成森林

总之，有在概念上是四个并行树结构，用于为渲染提供稍有不同需求的服务：

1.DOM树，就是我们基本的保留模型<br> 
2.RenderObject树，具有与DOM树中可见节点1：1的映射 RenderObjects知道怎么paint其相应的DOM节点。<br> 
3.RenderLayer树，由RenderLayers组成并映射到的RenderObject树中的RenderObject。其间映射关系为多对1，就好比一个RenderObject要么给其分配一个RenderLayer，要么他的最近祖先RenderObject有一个RenderLayer。RenderLayer树维护着z轴排序的层。<br>
4.GraphicsLayer树，一个GraphicsLayers映射一个或多个RenderLayers。

在Chrome与Chrome合成器层中每个GraphicsLayer都有Web*Layers的实现。正是这些最终的“CC层”（CC = Chrome compositor）合成器才知道如何进行操作。

从这里开始本文档中的“layer”是指一个通用的CC layer，因为对于硬件合成来说这些是最有趣的 - 但不要被愚弄，在其他地方，当有人说“layer”，他们可能会被提及的任何上述的结构中！

![The Compositing Forest](https://lh5.googleusercontent.com/eLlgjXLQbW-c2JWxfK0VxXbmV2-D90ub4dXYMblpHdyVyM_6a12JdfQKiokPrTcB-2UOn284A3teHK4P0e9Xlmm5uSxA7utj6Pggf7T-J7uxwc8iBPopZ7vtcCRYGvtteQ)

#### 合成森林

现在，我们已经（暂时的）覆盖了Blink中DOM链接到合成器的数据结构，我们已经准备好并认真的去探讨合成器的真生了。
