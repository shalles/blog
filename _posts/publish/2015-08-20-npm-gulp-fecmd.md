---
layout: post
category : [Tools, Publish]
title : "gulp-fecmd"
tagline: "利其器"
tags : [require, gulp, CMD, gulp-fecmd, FE]
excerpt:  前端也可以像nodejs一样使用require moduel.exports的方式来导出和引用文件且支持模板等。
---
{% include JB/setup %}

# gulp-fecmd

### what's gulp-fecmd
gulp-fecmd is a tool that will help FE coding js with CMD(Common Module Definition) free without quote any third-party library in your program;

### install

```
npm install gulp-fecmd
```

**`version`** 1.0.6

add support bower module, and more please look down "gulpfile.js" and "a.js"

**`version`** 1.0.7

support .json

**`version`** 1.0.8

support .es6 
more infomation [ https://babeljs.io/docs/learn-es2015/ ](https://babeljs.io/docs/learn-es2015/)

### Documentation

```js
//gulpfile.js

var fecmd = require('gulp-fecmd');

gulp.task('scripts', function() {
    var data =  gulp.src('js/a.js')
                    .pipe(sourcemaps.init());



    // you should use it before minify or uglify and ...
    // support bower module like 
    // fecmd({
    //      modulePath: "bower directory"
    // })
    // if you don't give modulePath, default is the directory in ".bowerrc" file 
    // or the folder bower_components in you build path but all the first is you 
    // scripts folder then bower module
    data = data.pipe(fecmd()); 
    


    config.minify && (data = data.pipe(uglify()).pipe(sourcemaps.write()));
    config.livereload && data.pipe(livereload());
    config.version ?
        data.pipe(rev())
            .pipe(gulp.dest(config.scripts.exp))
            .pipe(rev.manifest('js-map.json'))
            .pipe(gulp.dest(config.tmp)) : 
        data.pipe(gulp.dest(config.scripts.exp));
});

```

**program file**

```html
<!-- file index.html -->
<script src="js/a.js"></script>

```

```js
// file a.js

var b = require('lib/b.js'); // '[./]lib/b[.js]'
var tpl = require('tpl/xx.tpl'); //return a string
var json = require('data/data.json'); //return the Object
var es6 = require('lib/file.es6'); // return es5 code
// or
// require('c.js');
// require('d');
// 
// if your version is 1.0.6 or newer you can quote module 
// from bower module lick this require('jquery'), without 
// extname and without a filename jquery or jquery.js file
// in the same dir with a.js
require('jquery');


/* do something */
var console.log(b.c);

```

```js
// file lib/b.js

// other code do something
// such
// require()...
// var a,b,c...
// function(){} ...

//export your module
//*
module.exports = {
    c: 2,
    cc: 23
}
/*/
//or
exports.c = 2;
exports.cc = 23;
//*/
//
```
**es6**

```js
// file.es6

class Calc {
    constructor() {
        console.log('Calc constructor');
    }
    add(a, b) {
        return a + b;
    }
}

module.exports = Calc;

// usage
// var c = new Calc();
// console.log(c.add(1, 2));
```


**template**

require support template (*.tpl) like this file "xx.tpl"
and export a string

```html
<div>
    {{#list}}
    <span>{{supportTemplate}}</span>
    {{/list}}
</div>
```
export
```js
"<div>\n    {{#list}}\n    <span>{{supportTemplate}}</span>\n    {{/list}}\n</div>"
```

###实现原理

其实很简单, 我们来看一下导出文件的代码

```js
;(function(___CONTEXT___){
    if (!___CONTEXT___.___MODULES___) {
        ___CONTEXT___.___MODULES___ = {};
    };
    function convertToID(path){
        return path.replace(/[^a-zA-Z0-9]/g, "");
    }
    function require(path){
        var module = ___CONTEXT___.___MODULES___[convertToID(path)];
        if(!module){ console.log("导出文件有问题"); return;}
        if(module.fn && !module.exports){
            module.exports = {};
            module.fn(require, module.exports, module, window);
            delete module.fn;
        }
        return module.exports;
    }

    // file "/src/scripts/lib/cookieStorage.js" to module[srcscriptslibcookieStoragejs]
    ___CONTEXT___.___MODULES___["srcscriptslibcookieStoragejs"] = {
        path: "/src/scripts/lib/cookieStorage.js",
        fn: function(require, exports, module, window, undefined) {
            // 对应path的文件实际内容
            // ...
            module.exports = cookieStorage;
        }
    }

    // ... 其他模块
    
    // file "/src/scripts/lib/localStorage.js" to module[srcscriptsliblocalStoragejs]
    ___CONTEXT___.___MODULES___["srcscriptsliblocalStoragejs"] = {
        path: "/src/scripts/lib/localStorage.js",
        fn: function(require, exports, module, window, undefined) {
            // 对应path的文件实际内容
            var cookieStorage = require("/src/scripts/lib/cookieStorage.js");
            // ...
            // ...
            module.exports = LocalStore;
        }
    }
    
    // 对于模板 导出如下
    
    // file "/src/scripts/template/code.tpl" to module[srcscriptstemplatecodetpl]
    ___CONTEXT___.___MODULES___["srcscriptstemplatecodetpl"] = {
        path: "/src/scripts/template/code.tpl",
        fn: function(require, exports, module, window, undefined) {
            
            module.exports="\n    // file \"{{ path }}\" to module[{{ id }}]\n    ___CONTEXT___.___MODULES___[\"{{ id }}\"] = {\n        path: \"{{ path }}\",\n        fn: function(require, exports, module, window, undefined) {\n            \n            {{ code }}\n            \n        }\n    };\n\n"
            
        }
    };

    // file "/src/scripts/app-bmap.js" to module[srcscriptsappbmapjs]
    ___CONTEXT___.___MODULES___["srcscriptsappbmapjs"] = {
        path: "/src/scripts/app-bmap.js",
        fn: function(require, exports, module, window, undefined) {
            
            var LocalStorage = require("/src/scripts/lib/localStorage.js");
            // ... 
        }
            
    // 主文件执行
    require("/src/scripts/app-bmap.js");

})(this);

```