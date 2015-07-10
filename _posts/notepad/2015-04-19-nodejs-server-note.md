---
layout: post
category : [js, Node]
title: "nodejs学习笔记"
tagline: "notepad"
tags : [node, nodejs, node.js, express, koa]
excerpt: 这里只是一个入门分享的介绍，使用nodejs应用现在主流的两种框架快速搭建web服务。
---

{% include JB/setup %}

2015.04.17 payued分享

####what’s Node

*js运行环境平台*

    Node.js采用C++语言编写而成，内嵌v8引擎。node.js 是一个 platform build on Chrome’s JavaScript runtime。用于方便地搭建响应速度快、易于扩展的网络应用

*采用事件驱动，非阻塞I/O模型*

    轻量和高效，非常适合在分布式设备上运行的数据密集型的实时应用。

*单线程*

    它通过事件轮询（event loop）来实现并行操作，对此，我们应该要充分利用这一点 —— 尽可能的避免阻塞操作，取而代之，多使用非阻塞操作，"无法很好的利用多核CPU?Yes/No"。 

    由HTML5之前浏览器的js单线程致使UI和循环卡死想当然的认为NodeJS封装了内部的异步实现后，导致程序员无法直接操作线程，也就造成所有的业务逻辑运算都会丢到JavaScript的执行线程上，这也就意味着，在高并发请求的时候，I/O的问题是很好的解决了，但是所有的业务逻辑运算积少成多地都运行在JavaScript线程上，形成了一条拥挤的JavaScript运算线程。

*提供了child_process模块、cluster模块来解决多线程的要求*

    一个Node进程就能很好的解决密集I/O的情况下，Node子进程可以当作常驻服务来解决运算阻塞的问题https://nodejs.org/api/child_process.html 

*cluster集群模块*

    cluster可以用来让Node.js充分利用多核cpu的性能，同时也可以让Node.js程序更加健壮，官网上的cluster示例已经告诉我们如何重新启动一个因为异常而奔溃的子进程。https://nodejs.org/api/cluster.html 

    cpu密集强迫症也可以使用C/C++模块扩展

```js
var cluster = require('cluster');
var http = require('http');
var numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
  // Fork workers.
  for (var i = 0; i < numCPUs; i++) {
    cluster.fork(); //child_process.fork()
  }

  cluster.on('exit', function(worker, code, signal) {
    console.log('worker ' + worker.process.pid + ' died');
  });
} else {
  // Workers can share any TCP connection
  // In this case its a HTTP server
  http.createServer(function(req, res) {
    res.writeHead(200);
    res.end("hello world\n");
  }).listen(8000);
}
```

*看例子*(2min)

```
$ npm init
$ npm install
```

####what’s Express

*Web 应用*

    Express 是一个简洁、灵活的 Node.js Web 应用开发框架, 它提供一系列健壮的特性，帮助你创建各种 Web 和移动设备应用。

*性能*

    Express 不对 Node 已有的特性进行二次抽象，我们只是在它之上扩展了 Web 应用所需的基本功能。

*API*

    丰富的 HTTP 快捷方法随你信手拈来，让你创建健壮的 API 变得既快速又简单。

```
$ npm install express --save
$ npm install express-generator -g
```
一个简单的官方例子

```js
var express = require('express');
var app = express();

app.get('/', function (req, res) {
  res.send('Hello World!');
});

var server = app.listen(3000, function () {
  var host = server.address().address;
  var port = server.address().port;

  console.log('Example app listening at http://%s:%s', host, port);
});
```

*API文档*

[http://expressjs.com/4x/api.html ](http://expressjs.com/4x/api.html)

*看例子*(5min)


####what’s Koa

由 Express 原班人马打造的 koa，致力于成为一个更小、更健壮、更富有表现力的 Web 框架。使用 koa 编写 web 应用，通过组合不同的 generator，可以免除重复繁琐的回调函数嵌套，并极大地提升常用错误处理效率。Koa 不在内核方法中绑定任何中间件，它仅仅提供了一个轻量优雅的函数库，使得编写 Web 应用变得得心应手。

*安装启动*

```
$ npm install koa
$ node --harmony app.js
```

*编写级联代码*

当程序运行到 yield next 时，代码流会暂停执行这个中间件的剩余代码，转而切换到下一个被定义的中间件执行代码，这样切换控制权的方式，被称为 downstream，当没有下一个中间件执行 downstream 的时候，代码将会逆序执行
在看代码之前我们先来看一个ES6 generator的概念

```js
// This is a generator function. The star preceding the function name means
// it will return a 'generator' object, even though none of the code inside
// explicitly says so.
function *getAllSquareNumbers() {
    for (var i = 1; ; i++) {
        // Every time we 'yield', this function's execution pauses until
        // the generator is restarted by a call to 'next' (see below).
        yield i * i;
    }
}

// Now let's fetch some valuesvar generator = getAllSquareNumbers();
console.log(generator.next().value); // Outputs '1'
console.log(generator.next().value); // Outputs '4'
console.log(generator.next().value); // Outputs '9'
console.log(generator.next().value); // Outputs '16'

// We could go on. That's enough.

```

*生成器详解(2min)*：

[http://blog.stevensanderson.com/2013/12/21/experiments-with-koa-and-javascript-generators/ ](http://blog.stevensanderson.com/2013/12/21/experiments-with-koa-and-javascript-generators/)

```js
var koa = require('koa');
var app = koa();

// x-response-time
app.use(function *(next){
  // (1) 进入路由
  var start = new Date;
  yield next;
  // (5) 再次进入 x-response-time 中间件，记录2次通过此中间件「穿越」的时间
  var ms = new Date - start;
  this.set('X-Response-Time', ms + 'ms');
  // (6) 返回 this.body
});

// logger
app.use(function *(next){
  // (2) 进入 logger 中间件
  var start = new Date;
  yield next;
  // (4) 再次进入 logger 中间件，记录2次通过此中间件「穿越」的时间
  var ms = new Date - start;
  console.log('%s %s - %s', this.method, this.url, ms);
});

// response
app.use(function *(){
  // (3) 进入 response 中间件，没有捕获到下一个符合条件的中间件，传递到 upstream
  this.body = 'Hello World';
});

app.listen(3000);
```

*koa中间件(1min)：*

[https://github.com/turingou/koa-guide#%E4%B8%AD%E9%97%B4%E4%BB%B6middleware ](https://github.com/turingou/koa-guide#%E4%B8%AD%E9%97%B4%E4%BB%B6middleware)

*koa的路由中间件*

```
$ npm install koa-route
```

```js
var _ = require('koa-route');
var koa = require('koa');
var app = koa();

var db = {
  tobi: { name: 'tobi', species: 'ferret' },
  loki: { name: 'loki', species: 'ferret' },
  jane: { name: 'jane', species: 'ferret' }
};

var pets = {
  list: function *(){
    var names = Object.keys(db);
    this.body = 'pets: ' + names.join(', ');
  },

  show: function *(name){
    var pet = db[name];
    if (!pet) return this.throw('cannot find that pet', 404);
    this.body = pet.name + ' is a ' + pet.species;
  }
};

app.use(_.get('/pets', pets.list));
app.use(_.get('/pets/:name', pets.show));

app.listen(3000);
console.log('listening on port 3000');
```

看例子(5min)

… …

API文档

[https://github.com/turingou/koa-guide ](https://github.com/turingou/koa-guide)


