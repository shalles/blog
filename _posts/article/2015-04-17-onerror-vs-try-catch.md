---
layout: post
category : js
title: "onerror vs try-catch"
tagline: "no error"
tags : [window.onerror, try catch]
excerpt: 由前端页面运行时脚本出错报警的问题的两个解决方案1.使用try catch; 2.使用window.onerror;引出的思考
---
{% include JB/setup %}

###onerror vs try-catch

```js
/*[export]*/
/*
 * filename:    index.js
 * author:  shalles
 * E-mail:  shalles@163.com
 * CreateTime:  2015-04-17 10:36:18
*/

//onerror全局错误捕获处理函数，会终止同步代码
window.onerror=function(e){
    console.log("catch a error")
}

//异步测试代码
setTimeout(function(){
    console.log("The async code before an error code could execute normal yet")
}, 1000);

//带有错误的测试代码
function errorCode(){
    for (var i = Things.length - 1; i >= 0; i--) {
        Things[i]
    };
}

//*/
//catch中没有错误则不会终止代码
try{
    errorCode();
} catch(e){
    //添加处理异常代码

    //throw Error("error create in try catch, but you can named it free")
    
    //添加报警代码
    console.log("catch by try-catch");
}
/*/
errorCode();
//*/

//使用try catch捕获前面的错误后， 后面的代码可以正常执行
//而onerror捕获的则不会执行，且使用try catch捕获到的错误
//不会再被onerror捕获。
console.log("the code after error also run to here");
```

因此为了兼顾到onerror提供信息全面的优势,和try catch捕
获错误而不干扰后续代码的执行, 又兼顾try catch捕获到的
错误不会再被onerror捕获。那我们该怎么做呢？
其实我们分开来想问题, try catch主要是捕获一些我们能预
想到的错误即异常。并在catch中可嵌套的添加备用方案。
window.onerror主要是用来捕获一些运行时预料不到的问题，
当然try-catch也会捕获到不在预料中的问题, 人总有疏忽。
那现在他们有了共同点,就是可以捕获没有预料到的问题, 这
也是为什么要写这个测试《onerror vs try-catch》的目的。
其实没有那个更好,因为他们目的没有多少交集。在写代码时
有备选方案(某段代码错了就错了不影响后续执行的, 此时的
catch不处理也是一种备选方案) 的该处理的异常还是用try-
catch处理, 在加监控时要捕获全局错误的需要详细错误信息
的还是用window.onerror, 互不干扰。为了监控的话,折中的
我们在catch中也阔以把一些错误发送给监控。