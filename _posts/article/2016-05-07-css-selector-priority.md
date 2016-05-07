---
layout: post
category : [Css]
title : "css 选择器优先级匹配测试"
tagline: "skill"
tags : [css, selector, priority]
excerpt: 在开发中我们经常会遇到元素样式覆盖问题，有可能是前人些的组件在我们写的时候需要定制的覆盖一下，有可能功能切换产生的覆盖。
---
{% include JB/setup %}

### 摘要

在开发中我们经常会遇到元素样式覆盖问题，有可能是前人些的组件在我们写的时候需要定制的覆盖一下，有可能功能切换产生的覆盖。当然我们可以尽可能的避免功能切换上的样式覆盖，比如指定不同class。

很常见的一种场景是，我们在开发中有一些分支，且这些分支在样式上有一定的差距，如果我们一个元素一个元素的去更改class也是一种灾难。通常我们会在这些可能变化的元素的公共父级去改变这个class标记

如：

```scss
.main{
	color: #fff;
}
.main .a{
	color: #ff0;
}
.main .b{
	color: #f0f;
}

.main.branch2{
	color: #eee;
}
.main.branch2 .a{
	color: #ee0;
}
.main.branch2 .b{
	color: #e0e;
}
```

当然css选择器的优先级不止能做这个，了解css选择器的优先级我们才能更加正确有效的coding

### 先统一一下概念

**样式规则** `selector { property: value; ... }`

依次是`选择器`=>`样式名`=>`样式值` ; `样式名`=>`样式值` ...

样式规则的匹配顺序: `继承的样式`=>`用户代理（浏览器）`=>`用户规则（样式表）`=>`HTML内嵌样式`

### 实践测试优先级

```html
<div id="a1" class="a1">a1
  <div id="a2" class="a2">a2
    <div id="a3" class="a3">a3
      <div id="a4" class="a4">a4
        <div id="a5" class="a5">a5
          <div id="a6" class="a6">a6
            <div id="a7" class="a7">a7</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```

**下面每一个样式规则分类都是后面的比前面的优先级高**

这里只有六层，那我们假定 important: 1000; id: 100; class: 10; tag: 1;

当然这里是假定，总的来说important比id无限大 id比class无限大 class比tag无限大

```scss
//继承
.a1{
    color: #06f;
} 
.a3{
    color: #0f6;
}

//a1 顺序
.a1::after {
  content: '.0';
}
.a1::after {
  content: '0.';
}

// 后面测试权值
// a2 
.a2::after {
  content: '0';
}
div .a2::after {
  content: '1';
}
.a1 .a2::after {
  content: '10';
}
#a1 .a2::after {
  content: '100';
}

// a3
#a1.a1 .a3::after {
  content: '110';
}
.a1#a1 .a3::after {
  content: '110.';
}

// a4
.a4::after {
  content: '0';
}
div .a4::after {
  content: '1';
}
div div .a4::after {
  content: '2';
}
// class id (same as)

// a5
.a5::after {
  content: '0';
}
.a1 .a5::after {
  content: '10';
}
div div div div div.a5::after {
  content: '5';
}
//a4上的class 同样需要加入计算的
// div div div #a4.a4::after {
//   content: '103';
// }

// a6
.a6::after {
  content: '0';
}
#a1 #a2 #a3 #a4 #a5 .a6::after {
  content: '500';
}
#a1 #a2 #a3 #a4 #a5 div.a6::after {
  content: '501';
}
#a1.a1 #a2 #a3 #a4 #a5 .a6::after {
  content: '510';
}

// important 
#a1.a1 #a2.a2 #a3.a3 #a4.a4 #a5.a5 #a6.a6 .a7::after {
  content: '660';
}
.a7::after {
  content: '1000'!important;
}
// 加上important 依然尊重以上规则
.a7{
  color: #8f8!important;
}
#a7.a7{
  color: #88f!important;
}
```
### 测试Demo
<iframe height='320' scrolling='no' src='//codepen.io/shalles/embed/GZzqZZ/?height=320&theme-id=0&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='http://codepen.io/shalles/pen/GZzqZZ/'>GZzqZZ</a> by shalles (<a href='http://codepen.io/shalles'>@shalles</a>) on <a href='http://codepen.io'>CodePen</a>.

### 一些优化提醒

1. 不要写空标签`style`, 因为浏览器同样会为空style或link空css文件创建样式表
2. 不要写空样式规则`#id{}`, 因为浏览器同样会为空样式规则创建该样式规则对象，只是没有对应属性键值对
