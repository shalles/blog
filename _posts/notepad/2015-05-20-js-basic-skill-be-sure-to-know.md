---
layout: post
category : js
title: "不能不知道的js基础知识"
tagline: "no error"
tags : [javascript, 基础, js, 不能不知道]
excerpt: 前端是一个很容易上手，但很多人做了很久的前端还是没有掌握javascript的一些重要的基础知识
---
{% include JB/setup %}

前端是一个很容易上手，但很多（兴趣点不在前端而是为了谋求一份高薪而容易上手的工作）人做了很久的前端还是没有掌握javascript的一些重要的甚至很基础的知识。

这里多说一点，大家一定要把兴趣放在前端知识上，就算HTML5/CSS3,或是nodejs没有出来之前前端能做要学的东西还是很多了,你确定你写的代码每一步怎么解析编译执行的吗，你知道换个语法（或写法），或者换个行加不加`;`对代码的影响吗，你知道不用库，不用框架怎们开发吗，你知道你用的库和框架的实现原理吗，你知道浏览器怎么渲染的吗，你知道怎样才能利用硬件加速渲染吗？你写的代码用GPU渲染就一定比CPU渲染性能好吗？......

####<a id="arguments"></a>arguments是一个类似数组的Arguments对象

```js
(function(){
    console.log(Object.prototype.toString.call(arguments));
})(1,2,3,4)

//输出[object Arguments]
```

####判断语句中使用bool == variable好还是直接使用variable或！varable

我们来看看

```js
> [0] == true
false
> !![0]
true
```

####兼容于ASCII和ISO标准的Unicode编码

>Unicode的UTF-8编码与ASCII字符是兼容，并且被许多程序所支持。前128个Unicode字符与ASCII字符一一对应，并具有相同的字节值。从U+0020至U+007E的Unicode字符等价于从0x20到0x7E的ASCII字符。ASCII支持拉丁字母并使用7位字符集，而UTF-8的每个字符占用一到四个8位组(8位组octet，即1个字节或8位)，这样可以表示数百万个字符。另一种编码标准，UTF-16，使用两个字节来表示Unicode字符。转义序列允许UTF-16用4个8位组表示Unicode字符， ISO UCS-2（通用字符集）则使用两个字节。

