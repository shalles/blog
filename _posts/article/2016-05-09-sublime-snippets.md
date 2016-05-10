---
layout: post
category : [Tools, Sublime]
title : "sublime snippets 让写代码飞起来"
tagline: "利其器"
tags : [sublime, snippets, coding fast]
excerpt:  写代码的时候，我们总会有一些代码常用，但又不好记，也没有必要记得代码片段。这个时候我们可能会保存起来，也可能利用IDE的code snippet功能存起来备用，现在来看看sublime的snippet使用
---
{% include JB/setup %}

写代码的时候，我们总会有一些代码常用，但又不好记，也没有必要记的代码片段（有一些功能我们可能在某一个时间段经常会用到，于是自己或看开源代码理解或重写了一个自己喜欢的实现方式，但我们不可能一直都有当时的状态去思考那么多）。这个时候我们可能会保存起来，也可能利用IDE的code snippet功能存起来备用，以前用visual studio的code snippet感觉很方便，现在来看看sublime的snippet怎么使用

其实很简单

### Start

`Browser Packages`=>`Packages`=>`User`=>`Snippets`(或创建)

我们的snippet文件以.sublime-snippet为扩展后缀

```html
<snippet>
    <content><![CDATA[Type your snippet here]]></content>
    <!-- Optional: Tab trigger to activate the snippet -->
    <tabTrigger>xyzzy</tabTrigger>
    <!-- Optional: Scope the tab trigger will be active in -->
    <scope>source.python</scope>
    <!-- Optional: Description to show in the menu -->
    <description>My Fancy Snippet</description>
</snippet>
```

### 编写snippet文件

以我的viewport snippet为例`vp.sublime-snippet`

```html
<snippet>
    <content>
<![CDATA[
<meta content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no" name="viewport">
]]>
    </content>
    <!-- Optional: 当输入tabTrigger中的内容后按tab键触发snippet执行 -->
    <tabTrigger>vp</tabTrigger>
    <!-- Optional: Scope是可以触发改代码片段的文件类型 -->
    <scope>text.html</scope>
    <!-- Optional: 该snippet的描述 因为在输入vp后再自动填充列表里面会列出所有可能的单词或 -->
    <description>viewport device mobile</description>
</snippet>
```

保存以上文件后。当在Sublime中编辑html文件的时候我们只需要输入`vp`然后按tab键则会在当前位置添加一下代码

```
<meta content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no" name="viewport">
```

### 进阶

**光标**

我们可以在代码片段中加入$ + number（如：$1 $2）来实现多光标插入。 如：代码片段

```scss
.sub1{
    width: $1;
    height: $2;
}
.sub2{
    width: $1;
}
```

执行（保存.sublime-snippet）后一开始两个`$1`处会同时出现输入光标，输入完后按`tab`键光标自动移到$2的位置接着输入

**palceholder**
vp
```
${1:20px}
```

如上面的$1换成`${1:20px}`,执行后则会出现选中的`20px`

**系统/自定义变量**

...用处不是很大，且支持目前实现不是很好

