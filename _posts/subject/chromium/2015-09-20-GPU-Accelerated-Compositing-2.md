---
layout: post
category : [Browser, Chromium, RT]
title : "在Chrome中的GPU加速合成(二)"
tagline: "利其器，伪翻译"
tags : [浏览器, browser, Chromium, Chrome, webkit, blink]
excerpt: 在Chrome中的GPU加速合成,这份文件提供了背景和细节上的Chrome浏览器硬件加速合成的实现。
---
{% include JB/setup %}

## 第2部分：合成器

Chrome的合成器是一种用于管理GraphicsLayer树和协调框架生命周期的软件库。它的代码放在[src/cc](https://code.google.com/p/chromium/codesearch#chromium/src/cc/)目录，blink外层。

#### 合成器简介

回想一下，渲染发生在两个阶段：首先是paint，然后是合成。这使得合成器可以在每个合成层的基础上执行额外的工作。例如，合成器是负责在合成自生之前应用必要的转换（如被该层的CSS的transform属性指定）得到每个合成层的位图。另外，由于层的painting从合成过程中解耦，这些层中的一个层无效只会导致单独重绘（repainting）和合成这个层的内容。

每次浏览器需要做一个新的画面，合成器draw。请注意，这混乱术语的区别：`draw`是合成器组合层（合成层和普通位图层）到最终的屏幕图像; 而`paint`是普遍了层后端操作的总体（位图用软件光栅化;纹理在硬件光栅化）。

#### GPU是什么呢？

那么，GPU在此种是怎样发挥作用的呢？合成器可以使用GPU来执行它的draw​​步骤。这是自旧的软件渲染模式， 即在渲染过程中通过（通过IPC和共享内存）位图与页面内容的切换到浏览器进程显示的一个重要开端（请参见“传统的软件渲染路径”附录更多关于该怎么做）。

在硬件加速体系结构，合成发成在GPU中，通过调用特定于平台的3DAPI（Windows 中用D3D; GL在任何平台都适用）。渲染器的合成器本质上使用GPU来绘制页的矩形区域（即所有那些合成的层，都被根据层树的变换等级相对于视口（viewport）定位）转换成一个位图，就是最后的页图像。

**插曲：GPU 进程**
在我们去任何进一步的探索GPU命令的合成器生产，其重要的是要了解如何在渲染过程中发出任何命令到GPU。在Chrome的多进程模式，我们有一个专门的程序完成这个任务：GPU进程。GPU进程的存在主要是出于安全考虑。请注意，Android是一个例外，Chrome浏览器使用一个进程内含式GPU来实现就像浏览器进程的一个线程一样运行。Android上的GPU线程的其他行为方式和在其他平台上的GPU进程一样。

由它的沙箱的限制，该渲染进程（其中包含Blink和合成器cc的一个实例）不能通过由OS（GL / D3D）提供的3DAPI, 直接发出调用的。处于这个原因，我们使用一个分离的进程来访问该设备。我们称这个进程为GPU进程。GPU的进程是专门设计用来提供给来自渲染器沙箱或更严格的Native Client的 “监狱”的访问访问系统的3DAPI。它通过一个客户端-服务器模型的工作原理如下：

![GPU进程](https://lh5.googleusercontent.com/w3susKkBaBl5_bWQfQpEFeyIwz6rON17mw1gqYzX5PzQx-j9rV8rnpo0ABHDGwpJRKk7ErEHhUUjjJtCMdDaN_nketqM3dT7_bq3zrtputtDbYtzQS_e5wyVV9_14kmDzw)

1. 客户端（代码运行在渲染器里或在NaCl模块里），与直接发出调用系统API相反，序列化（调用命令）并将其放入一个环形缓冲区（命令缓冲区）存储在内存来让自身与服务器进程共享。
2. 服务器（GPU进程运行在限制较少的沙箱，可以访问该平台的三维的API）从共享存储器取出序列化命令，分析它们，并执行适当的图形调用。


#### 命令缓冲区

被GPU进程接受的命令被格式化近似GL ES2.0 API（例如有一个命令相当于glClear，另一个相当于glDrawArrays）。由于大多数的GL呼叫没有返回值时，客户端和服务器可以尽可能的异步工作来保持性能开销相当低。在客户端和服务器间的任何必要的同步，例如在客户端通知服务器有额外的工作要完成，都是通过一个IPC机制来处理。

从客户的角度来看，应用程序既可以直接写入命令到命令缓冲区也可以使用GL ES 2.0 API，通过我们提供的幕后序列化的一个客户端库。为了方便合成器和WebGL目前都在使用GL ES客户端库。在服务器端，通过命令缓冲区接收到的命令被转化为通过ANGLE调用桌面OpenGL 或D3D。

#### 资源共享和同步

在客户机和服务器之间，除了 ​​对command buffer提供存储，Chrome使用共享内存去传递较大的资源，例如纹理位图，顶点数组等。请参阅[命令缓冲区文档](http://www.chromium.org/developers/design-documents/gpu-command-buffer)，所有关于该命令的格式和数据传输。

另一种结构，被称为[mailbox](http://src.chromium.org/viewvc/chrome/trunk/src/gpu/GLES2/extensions/CHROMIUM/CHROMIUM_texture_mailbox.txt)，提供一个在命令缓冲区之间共享纹理和管理他们的寿命的方法。该mailbox是一个简单的串标识符，可以为任何命令缓冲区附加（消耗）一个局部纹理id，然后通过该纹理的id别名访问。每个以这种方式附加的纹理id会在真实文理下保持着一个参考，而一旦所有参考都被通过删除局部纹理的id方式释放时，其实质地也遭到破坏。

[share points](http://src.chromium.org/viewvc/chrome/trunk/src/gpu/GLES2/extensions/CHROMIUM/CHROMIUM_sync_point.txt)同步点是用来提供在希望通过mailbox共享纹理的命令缓冲区之间无阻塞同步。插入的命令缓冲区A中的同步点，插入一个同步点到命令缓冲区A,然后等待命令缓冲去B并确保你下一步将要插入B的命令不会在已插入的A的同步点之前运行（加锁信号量）。

#### 命令缓冲多路​​复用

目前，Chrome针对每个浏览器实例使用一个简单的GPU进程来服务所有渲染进程和任何插件进程。GPU的处理可以在多个命令缓冲区之间复用，每一个都与自己的渲染上下文相关联。

每个渲染可以有GL的多种来源，例如WebGL的`canvas`元素直接创建GL命令流。层的合成，其内容直接在GPU上创建的工作原理如下：与直接渲染到后端存储相反，它们渲染到纹理（使用帧缓冲对象）合成器上下文在渲染GraphicsLayer时抓取和使用。需要注意的是，为了使合成器的GL上下文能够获得由一个屏幕外GL上下文生成的纹理是很重要的（即GL上下文用于其它GraphicsLayers' FBOs），所有被GPU进程使用的GL上下文都被创建来共享资源。

这种结构是这样的：

![](https://lh3.googleusercontent.com/NkY6VcaKsuj5QKpLUTNerf5xJezkRbKN02q-1tY7YmMgwsoJDGyXcaBIG3vr7pitDqyztqZtLw5LW3Tx_iVzEgVlKQpoEArQyW2LOAwKsvy8BZJLJ37ERnubSqxVEgcEXA)


#### 总结

GPU进程的架构提供了多种优势，包括：

安全性：渲染逻辑的主要在沙箱渲染进程中，并获得了平台的3DAPI（仅限于GPU的过程）。
鲁棒性：一个GPU进程崩溃（例如，由于错误的驱动程序）时不会影响浏览器。
一致性：对标准化的OpenGL ES 2.0为与平台无关的浏览器的渲染API允许一个单一的，易于维护的代码库跨浏览器的所有操作系统端口。
并行：渲染器可以快速发出命令到命令缓冲区，并返回到CPU密集型的渲染活动，让GPU进程对其进行处理。由于这条管线我们可以在多核机器上很好地利用这两个进程，以及同时使用GPU与CPU。

有了这种表达的方式，我们可以回去解释GL命令和资源是怎样被渲染器的合成器生成的。

