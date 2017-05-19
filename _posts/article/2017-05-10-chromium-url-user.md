---
layout: post
category : [Js,Api]
title : "浏览器从用户输入url到看到界面"
tagline: "api"
tags : [chromium, chrome, webkit, blink, 浏览器, 输入url, 用户]
excerpt: 
---
{% include JB/setup %}

##### 概
author: shalles

### 浏览器冲url到页面展示到用户（以Chromium为例）
1. DNS预解析
  边输入边解析，有一个猜测自补过程
  - 浏览器缓存 chrome://dns/   chrome://net-internals/#dns
  - 系统host存储map
  - 路由器DNS缓存
  - 查找ISP DNS服务器(浏览器调用DNS客户端想DNS服务器发送包含主机名的请求，DNS即与UDP，端口53)
    + 本地DNS服务器（DHCP或用户自定义，公司有公司的，万网域名也提供了）
    + DNS服务其层次结构
      - 根服务器
      - 顶级域TLD服务器（com/net/edu/国家顶级域）
      - 权威域服务器 （大多公司或机构）
      - ...
    + 返回包含对应IP地址的报文->DNS客户端->浏览器
2. 浏览器用IP+路径参数访问对应服务器
  - 浏览器打包请求数据向服务器发起请求(向服务器HTTP(80)/HTTPS(443)对应服务进程发起TCP链接，三次握手SYN -> SYN SCK -> ACK)
  - 服务器根据请求参数返回不同响应数据
  - 浏览器根据不同的返回数据使用不同的解析器解析
  - 该解压的解压，另外HTTPS的情况
    + 浏览器将自己支持的一套加密方式传送给网站
    + 网站选择一组加密算法(对称和非对称)和hash算法，并将自身身份信息（包括非对称加密公钥，用于浏览器加密加密数据的对称密钥）以证书的形式返回给浏览器
    + 浏览器收到证书后验证证书合法性，验证通过或用户授权通过后浏览器生成随机对称密钥（之后用于加密传输数据），计算出`握手信息`的hash值，并用对称密钥对握手信息加密，用网站返回的公钥加密该对称密钥后一并发送给网站。
    + 网站接收到浏览器公钥加密的对称密钥后用对应私钥解密得到原始对称密钥，并用该密钥解密浏览器的`握手信息`，生成`握手信息`的hash值并验证该hash与浏览器传送是否一致，确认后用解密出来的对称密钥加密应答`握手信息`并生成握手信息的hash值一并发给浏览器
    + 浏览器用对称密钥解密握手信息，然后计算握手信息的hash值与服务器传送是否一致，确认后浏览器与网站就用只有双方知道的对称密钥加解密通信

3. 浏览器解析HTML为例（浏览器解析出HTML文本数据后）
  - 浏览器扫描HTML文本，匹配出文本中可能的资源加载链接，交由资源管理器加载资源并管理
  - 同时根据文档类型声明，HTML文本数据交由render的html解析器解析
    + 构建dom树，先构建根节点document节点->DOCTYPE节点然后html节点元素（不排除其间可能有其他任意节点，这些html标签外的节点可以被选择器选择、a标签加入links、img->images、iframe->frames等），然后深度遍历HTML标签
    + 当遍历到script标签没有指定异步async或delay属性则停止解析等待脚本资源加载解压等完毕然后交由javascript引擎解析并执行(阻碍dom构建是因为javascript可以操作dom,防止数据不一致，比如append或appendChild，向父元素追加一个子元素，而html解析器不暂停依然向父元素插入元素，这个js追加的元素的位置将不可控)
      - 另外还可以通过脚本创建影子DOM(在渲染果中包含在DOM树中不可直接遍历的DOM节点)
      ```js
      if (Element.prototype.createShadowRoot){
          shadowRoot = componentEle.createShadowRoot();
          shadowRoot.appendChild(statusBarEle);
          shadowRoot.appendChild(headerEle);
      } else {
          componentEle.appendChild(statusBarEle);
          componentEle.appendChild(headerEle);
      }
      domNode.appendChild(shadowRoot)
      // [**Demo**](https://github.com/shalles/FET-chrome-extention/blob/master/simulate-platform-header/src/js/content.js)
      ```
  
    + 当遇到其他外链资源，交由资源加载器对应的loader加载资源，不阻碍HTML的解析和DOM树的构建。
    + css由css解析器解析生成CSSOM和内部规则表示，以便之后构建RenderObject渲染对象（因为js可以操作CSSOM改变样式规则，css的加载和解析会阻碍js的执行，因此在script最好放到文档末尾，或异步加载）
    + 样式规则针对DOM可视节点（display不为none的节点）进行规则匹配（选择器优先级，先后顺序匹配覆盖，生成RenderStyle对象）。
    + 渲染引擎创建包含各自的renderStyle的RenderObject。RenderObject的layout方法根据renderStyle样式规则中的框模型进行布局计算，为了布局，可能还会为一下内联元素创建匿名RenderBlock。这样每个RenderObject对象都知道自己关联的DOM对象和渲染布局信息。
    + 渲染引擎参考DOM树结构创建RenderObject树(如上非一一对应)，然后根据元素层次创建RenderLayer，再根据RenderObject树创建RenderLayer树（同一层可能有很多RenderObject，因此是一个一对多关系，RenderLayer节点实际对应的还是一个RenderObject节点，RenderLayer的父节点则对应该RenderObject的父节点，且这对父子RenderObject不在同一RenderLayer，又因为RenderObject与DOM元素非一一对应，因此RenderLayer的父节点不一定是DOM树上对应的父节点）。创建RenderLayer的条件：
      - DOM树的根节点document对应的RenderObject:RenderView
      - document的根元素对应的RenderObject:RenderBlock
      - 显示指定位置：如position: absolute/fixed 或float: left/right等的RenderObject
      - 有透明效果的RenderObject： 如opacity、filter、alpha等
      - 有溢出：如overflow
      - canvas/video/audio等复合节点及影子DOM对应的RenderObject
    + RenderObject绘制过程 
      - 绘制所有块的边框和背景
      - 绘制浮动元素
      - 绘制前景（内容、轮廓）（内联元素的以上结构都在这一步绘制）
      - RenderLayer的绘制过程（反射层->背景层->负z层->层的子女的背景-浮动元素-前景内容轮廓->层轮廓->子女层->滤镜）
    + CPU渲染和GPU加速：
      - 软件渲染：根据层次把每一层的元素叠加绘制到一张位图（优：当更新区域小时，只需计算该区域重叠的元素）
      - 硬件渲染：引入合成层，对其进行必要的数据缓存（优点之处）。渲染引擎根据以下规则决定哪些RenderLayer合成一个合成层
        + 设置了css3d(translate3d/translateZ)、透视（opacity）、剪裁（background-clip/position:clip/https://www.w3.org/TR/css-masking/#the-clip-path）、或者滤镜（filter）的RenderLayer(对应到DOM元素)
        + css过渡（transition）或动画（animation）的RenderLayer，优化只在动画过程中GPU加速（创建合成层）
        + vedio节点所在的RenderLayer
        + z-index比自己小的兄弟节点是合成层
    + DOM树构建完触发`DOMContentLoaded`事件，所有资源都加载完后触发`onload`事件
    + 以上过程中间或之后js可能对DOM节点CRUD，同样CSSOM对样式CRUD，这些更改适用以上规则渲染。