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

**`1如下code1与code2互斥`:**

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

例子看[codepen](http://codepen.io/xiaole/pen/OVMGLr)

###js 扩展语言

如coffee等 但个人不习惯python那种缩进方式的代码风格，不是我写代码的格式对不起，有时候没有括号 `{}` `[]` `()` 会逼死强迫症的。这里就不提供例子了。

如果真要缩短代码我们阔以改变一些语法

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

