---
layout: post
category : Skill
title : "写代码中的一些小技巧"
tagline: "利其器"
tags : [comment, coding skill, code]
excerpt: 写代码也是有技巧的，怎样快速的写代码，甚至怎样的注释或取消注释都能影响我们的工作效率
---
{% include JB/setup %}

###注释

**`1如下code1与code2互斥`:**vp

只需改动第一行的`//*/` 或 `/*/`则能达到互斥注释的效果

```
//*/
code1
/*/
code2
//*/
```

这个很好理解，在代码中注释看看效果立现。

###css 使用css扩展语言

如下要做一个冒泡事件捕获的Demo中， 为生成阶梯层次结构的dom

**html结构**

```
<div id="p1">
  <div>
    <div>
      <div id="pc1">
        <div id="pcc1">
          <div>
            <div id="c1"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```

**scss**

```
@mixin set-css-layered($selector, $clr, $i) {  
  $i: $i - 1;
  @if $i != 0 {
    #{$selector}{  
      border: 30px solid $clr;
    }
    @include set-css-layered(#{$selector} > div, $clr + #111111, $i);
  }
}
@include set-css-layered(#p1, #f60, 8);
```

生成css入下：(这个Demo的目的是看事件冒泡，开发产品的时候不能像我这么写哈)

```
#p1 {
  border: 30px solid #f60;
}

#p1 > div {
  border: 30px solid #ff7711;
}

#p1 > div > div {
  border: 30px solid #ff8822;
}

#p1 > div > div > div {
  border: 30px solid #ff9933;
}

#p1 > div > div > div > div {
  border: 30px solid #ffaa44;
}

#p1 > div > div > div > div > div {
  border: 30px solid #ffbb55;
}

#p1 > div > div > div > div > div > div {
  border: 30px solid #ffcc66;
}
```

scss的便利还有很多，清查看文档
[scss文档](http://sass.bootcss.com/docs/sass-reference/)

例子看[codepen](http://codepen.io/xiaole/pen/OVMGLr)

###js

```
//expression页包括logic expression多层嵌套，但最好不要
logic expression ? expression: expression;

// “||” 这个运算符和其他语言（c/c++）的不大一样,左边的值为假结果等于右边的值，为真则直接取它，不会自动类型转换为Boolean
config = config || default;
//类似的 “&&”. 如下模拟场景：传了callback采取调用 防止"undefined is not a function" error
callback && callback();

//转换int用位运算
~~variable
//转换Boolean
!!variable
//转字符串
"" + variable
```

**分支技术**

选择性赋值时我们可以先考虑，是否需要每次都要更新判断条件的需求，如果不是我们则用分支技术在初始化时用代码量来换取每次都判断的耗时

```js
/*
 * LocalStore 存储各种数据类型的数据到localStorage或cookie(已删除 业务只在移动设备上使用暂不考虑不支持localStorage的设备)
 * 
 * name 存储名
 * notStr:存储值不是字符串的所有类型，Boolean Number Object Array
 */
function LocalStore(prefix, notStr, isSession) {
    var storage = isSession ? window.sessionStorage : window.localStorage;
    prefix = prefix ? prefix + '_' : '';
    // 注意动态改变local name值后要用getItem(name)或setItem(name,value)回置
    // 选择性赋值时我们可以先考虑，是否需要每次都要判断的需求，如果不是我们则用分支技术在初始化时用代码量来换取每次都判断的耗时
    this.setItem = notStr ? function (name, value) {//value能为空 name不能为空
        try{
            storage.setItem(prefix + name, JSON.stringify(value), 360000);
        } catch(e){
            console.log(e);
        }
    } : function (name, value) {
        storage.setItem(prefix + name, value, 360000);
    }
    this.getItem = notStr ? function (name) {
        var val = storage.getItem(prefix + name);
        try {
            return JSON.parse(val);
        } catch(e){
            console.log(e);
        }
        return val;
    } : function (name) {
        return storage.getItem(prefix + name);
    }
    this.removeItem = function(name){
        return storage.removeItem(name);
    }
}

module.exports = LocalStore;
```
