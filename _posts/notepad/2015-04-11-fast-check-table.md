---
layout: post
category : js
title: "不能不知道的js基础知识"
tagline: "no error"
tags : [javascript, 基础, js, 不能不知道]
excerpt: 前端是一个很容易上手，但很多人做了很久的前端还是没有掌握javascript的一些重要的基础知识
---
{% include JB/setup %}


###nodeType
1. `Document` nodes have the value `9`.
2. `Element` nodes have the value `1`.
3. `Text` nodes have the value `3`.
4. `Comments` nodes are `8`.
5. `Document-Fragment` nodes are `11`.

###运算符优先级
|优先级| 运算符 | 描述 |
|:-:|:---:| :---|
|1|. [] ()| 字段访问、数组下标、函数调用以及表达式分组|
|2|++ -- - ~ ! delete new typeof void   |一元运算符、返回数据类型、对象创建、未定义值|
|3|* / %    |乘法、除法、取模|
|4|+ - +    |加法、减法、字符串连接|
|5|<< >> >>>|移位|
|6|< <= > >= instanceof |小于、小于等于、大于、大于等于、instanceof|
|7|== != === !==    |等于、不等于、严格相等、非严格相等|
|8|&    |按位与|
|9|^    |按位异或|
|10|\|  |按位或|
|11|&&  |逻辑与|
|12|\|\|    |逻辑或|
|13|?:  |条件|
|14|= oP=|赋值、运算赋值|
|15|,   |多重求值|

###support
|          属性         | IE7   |  IE8  |  IE9  | IE10  | IE11  |FireFox|Chrome |
| :-------------------- | :---: |:----: |:----: |:----: |:----: |:----: |:----: |
|ajax                   | true  | true  | true  | true  | true  | true  | true  |
|appendChecked          |`false`| true  | true  | true  | true  | true  | true  |
|boxSizing              | true  | true  | true  | true  | true  | true  | true  |
|changeBubbles          |`false`|`false`| true  | true  | true  | true  | true  |
|checkClone             | true  | true  | true  | true  | true  | true  | true  |
|checkOn                | true  | true  | true  | true  | true  | true  | true  |
|clearCloneStyle        | true  | true  |`false`|`false`|`false`| true  | true  |
|cors                   |`false`|`false`|`false`| true  | true  | true  | true  |
|cssFloat               |`false`|`false`| true  | true  | true  | true  | true  |
|deleteExpando          |`false`|`false`| true  | true  | true  | true  | true  |
|enctype                | true  | true  | true  | true  | true  | true  | true  |
|focusinBubbles         | true  | true  | true  | true  | true  |`false`|`false`|
|getSetAttribute        |`false`| true  | true  | true  | true  | true  | true  |
|hrefNormalized         |`false`| true  | true  | true  | true  | true  | true  |
|html5Clone             | true  | true  | true  | true  | true  | true  | true  |
|htmlSerialize          |`false`|`false`| true  | true  | true  | true  | true  |
|inlineBlockNeedsLayout | true  |`false`|`false`|`false`|`false`|`false`|`false`| 
|input                  | true  |`false`| true  | true  | true  | true  | true  |
|leadingWhitespace      |`false`|`false`| true  | true  | true  | true  | true  |
|noCloneChecked         |`false`|`false`|`false`|`false`|`false`| true  | true  |
|noCloneEvent           |`false`|`false`| true  | true  | true  | true  | true  |
|opacity                |`false`|`false`| true  | true  | true  | true  | true  |
|optDisabled            | true  | true  | true  | true  | true  | true  | true  |
|optSelected            |`false`|`false`|`false`|`false`|`false`| true  | true  |
|ownLast                |`false`|`false`|`false`|`false`|`false`|`false`|`false`|
|radioValue             |`false`|`false`|`false`|`false`|`false`| true  | true  |
|style                  |`false`|`false`| true  | true  | true  | true  | true  |
|submitBubbles          |`false`|`false`| true  | true  | true  | true  | true  |
|tbody                  |`false`| true  | true  | true  | true  | true  | true  |
