---
layout: post
category : [Style,Touch]
title: "css媒体查询"
tagline: "media"
tags : [css, media, 响应式布局, 媒体查询]
excerpt: 通过css的media来实现对不同媒介、设备定制不同适配样式的选择，是css实现响应式布局的关键
---
{% include JB/setup %}

**必要的META**
看另一篇


###小知识

**PPI:** 
>像素密度。每英寸物理屏幕上包含的css像素点的个数   [PPI对照表](http://screensiz.es/phone) 

**viewport**
>移动页面有两个viewport
    - `layout viewport` 文档原始布局，不随屏幕转动或放缩而变化
    - `virtual viewport`页面可视区域，可视区域会随缩放旋转移动的变化而变化的 

```html
<meta  name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0,  user-scalable=no,minimal-ui"/>
```

width: [pexel_value|device-width] viewport的宽度`[200, 10000]`，默认980px
height: [pexel_value|device-width] viewport的高度`[223,10000]`
initial-scale: float_value初始的缩放比例`(0，10]`
minimum-scale: float_value 可缩小比例临界值
maximum-scale: float_value 可放大比例临界值
user-scalable: [`yes`|`no`] 允许用户手动缩放
target-densitydpi: [`dpi_value`|`device-dpi`|`high-dpi`|`medium-dpi`|`low-dpi`] 目标屏幕的像素密度
minimal-ui: ios7.x safari中添加的隐藏地址栏和导航栏