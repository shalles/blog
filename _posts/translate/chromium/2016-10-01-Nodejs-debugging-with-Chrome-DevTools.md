---
layout: post
category : [Tools,Chromium,Translate]
title : "Node.js debugging with Chrome DevTools"
tagline: "伪翻译"
tags : [nodejs, chrome, DevTools, FE, 调试神器, chromium]
excerpt: 在chrome中调试node js是一种什么样的体验？
---
{% include JB/setup %}

## 在chrome中调试node js是一种什么样的体验？

终于可以统一在chrome中调试我们的nodejs代码了，前端代码，服务端代码一条龙服务。

### 需要怎么做

1. 安装node.js 6.3+

2. chrome 55+ 目前稳定版是53+，我们可以下载尝鲜版[chrome canary](https://www.google.com/intl/en/chrome/browser/canary.html)

3. 在chrome中开启DevTools的实验功能，[chrome://flags/#enable-devtools-experiments](chrome://flags/#enable-devtools-experiments)，重启使更改生效。

![开启DevTools实验功能]({{ BASE_PATH }}/assets/images/chromium/devtools/flags-devtools-experiments.png)

4. 打开DevTools Setting -> Experiments -> 连续按6此shift按键

![开启DevTools实验功能]({{ BASE_PATH }}/assets/images/chromium/devtools/setting_experiments.png)

5. 勾选Node debugging选项，你可能会看到更多的实验功能选项，更具自己的需要勾选，这里我们只测试nodejs debugging。

6. 在chrome中打开一个空tab，并开启DevTools

7. 在你的nodejs项目更目录下启动node调试，然后在DevTools的sources中查看你的node项目代码。打断点像调试前端js一样。


```shell
node --inspect index.js //index.js是你node项目的入口js 当然也可以调试单一的node文件
```

或者如下图，在chrome中直接打开红框中`chrome-devtools://devtools/remote/serve_file/@60cd6e859b9f557d2312f5bf532f6aec5f284980/inspector.html?experiments=true&v8only=true&ws=localhost:9229/node` 类似链接，这种方式不需要另行打开DevTools

![开启DevTools实验功能]({{ BASE_PATH }}/assets/images/chromium/devtools/start_node_debugging.png)

8. 在chrome另一个tab中打开你的前端页面，然后开始调试。

![开启DevTools实验功能]({{ BASE_PATH }}/assets/images/chromium/devtools/start_node_debugging_devtools.png)


