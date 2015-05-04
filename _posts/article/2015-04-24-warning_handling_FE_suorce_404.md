---
layout: post
category : js
title: "前端资源加载404监控解决方案一"
tagline: "FE source 404 warning"
tags : [suorce, 前端资源监控, 404]
excerpt: 前端资源文件加载404,未找到资源的错误的报警。404可以直接在服务端获取到，这里只提供了一种前端思想的简单解决方案和代码
---
{% include JB/setup %}

404可以直接在服务端获取到，这里只提供了一种前端思想的简单解决方案和测试代码，解决因网络问题等导致的样式加载超时等失败。

####测试代码，具体根据自己所应用场景实现

这里包含了运行时脚本错误的监控代码

```js
var WarningScriptBug = (function(){
    /*
     * 主要思想：
     * --运行时脚本错误使用window.onerror监控
     * --资源文件加载404使用在各个资源文件里面
     *   向监控文件发送注册加载成功标示;在监控
     *   css等样式文件时使用在dom中设置对应节点
     *   的font-family: cssfilename;(不加后缀)
     *   也可以使用其他属性标识，这只是提供一个
     *   思想。
     *
     * 兼容性：
     *   没怎么考虑，这里主要提供一种方案来探讨，
     *   简单实现，且因为低版本(<IE9)的onerror提
     *   供的信息也很少，没有最重要的调用栈信息，
     *   于try catch没多大优点，如果要考虑兼容性
     *   的话可以使用try catch即可
     *
     * 例子(监控资源404)：
     *   --样式资源文件中
     *      #sourcefile .site{
     *          font-family: site;
     *      }
     *   --对应样式的页面(html)中
     *      <div id="sourcefile">
     *          <i class="site"></i>
     *          <i class="sub1"></i>
     *          <i class="sub2"></i>
     *      </div>
     *    
     *   --脚本资源文件中
     *      //subscript.js的文件中添加
     *      window.scriptLoadedRegister("subscript.js");
     *  
     * 相关文章请参考：
     * http://shalles.github.io/blog/js/2015/04/23/warning_handling_javascript_runtime_bug/
     */
    function WarningScriptBug(config){
        this.src404 = config.src404 || false,
        this.runtime = config.runtime || true;
        this.sourceFileNames = config.srcfileNames;
        this.warningCallBack = config.warningCallBack;
        this.srcDom = config.srcDom;
        this.styleFileFlagDom = config.styleFlagDom;
        this.codeBlock = config.codeBlock;
        this.init();
    }
    WarningScriptBug.prototype = {
        init: function(){
            var self = this;
            //给其他脚本文件注册
            window.scriptLoadedRegister = function(filename){
                loadedDelete(self.sourceFileNames, filename);
            }
            this.runtime && warningPost(this.warningCallBack, this.codeBlock);
            this.src404 && this.ensureSourceLoaded();
        },

        warningPost: function(codeBlock){
            warningPost(this.warningCallBack, codeBlock);
        },

        ensureStylesLoad: function(){
            var cur, alreadyLoaded;
            this.srcDom = this.srcDom || document.getElementById("sourcefile");
            this.styleFileFlagDom = this.styleFileFlagDom || this.srcDom.getElementsByTagName("i")
            //不支持 则该方法无效
            if(!window.getComputedStyle) return;

            alreadyLoaded = this.styleFileFlagDom || this.srcDom.getElementsByTagName("i");

            //console.log("alreadyLoaded: ", alreadyLoaded);
            for(var i = 0, len = alreadyLoaded.length; i < len; i++){
                cur = window.getComputedStyle(alreadyLoaded[i],null).fontFamily + ".css";
                loadedDelete(this.sourceFileNames, cur);
            }
        },

        ensureSourceLoaded: function(){
            var self = this;
            window.addEventListener( "load", function(){
                self.ensureStylesLoad();
                self.srcDom.parentElement.removeChild(self.srcDom);
                ensureSourceLoad(self.sourceFileNames);
            }, false);
        }
    }

    function warningPost(callback, codeBlock){
        //*/
        window.addEventListener("error", function(e){
            callback && callback(e, e.message, e.filename, e.lineno, e.colno, e.error.stack);
        }, false);
        /*/
        try{
            codeBlock && codeBlock(); //代码块
        } catch(e){
            callback && callback(e);
        }
        //*/
    }

    function loadedDelete(sourceVector, filename){
        var idx = sourceVector.indexOf(filename)
        idx > -1 && sourceVector.splice(idx, 1);
    }

    function ensureSourceLoad(sourceVector){
        //没有加载完所有脚本则报错
        if(sourceVector.length > 0){
            throw new Error("404!! not load such files: " + sourceVector.toString());
        }
    }

    return WarningScriptBug;
})();

//使用
new WarningScriptBug({
    src404: true,
    runtime: true,
    srcfileNames: ["index2.js", "subscript.js","site.css", "sub1.css", "sub2.css"],
    warningPost: function(e){ //parameter: errorObj, message, filename, lineno, colno, stackTrace
        alert('Show Error by Alert: \nmessage:' + e.message + '\nfilename: ' + e.filename + '\nLine: ' + e.lineno
                    + '\nColumn: ' + e.colno + '\nStackTrace: ' +  e.error.stack);
            console.log('\nShow Error in Console: \nmessage:' + arguments[1] + '\nfilename: ' + 
                        arguments[2] + '\nLine: ' + arguments[3] + '\nColumn: ' +
                        arguments[4] + '\nStackTrace: ' +  arguments[5]);
            console.info(e);
        alert("ajax post");
    },
    srcDom: null,
    styleFlagDom: null,
});
/*
 * 下面是运行时错误检测测试
 */
function allNeedMonitoringCode(){
    var dd;
    for(dd = 1; dd < l; dd++){

    }
}
document.body.querySelector("#cliError").addEventListener("click", clickHandler, false);
//document.body.querySelector("#err404").addEventListener("click", click404Handler, false);

//*/
// 使用onerror
function clickHandler(){
    allNeedMonitoringCode();
}
```

