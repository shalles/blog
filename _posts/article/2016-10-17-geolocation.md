---
layout: post
category : [Skill,Js]
title : "Geolocation地图定位问题"
tagline: "skill"
tags : [geolocation, https, 地图定位, H5定位, 支付宝定位, 微信定位, 位置定位]
excerpt: 随着业界对web安全的重视，https取代http的进度再次推上浪尖。google等先驱对自家和其服务使用者强制或间接推动https的布局。google旗下geolocation定位在新版api中只支持https网站获取位置定位服务
---
{% include JB/setup %}


随着业界对web安全的重视，https取代http的进度再次推上浪尖。google等先驱对自家和其服务使用者强制或间接推动https的布局。google旗下geolocation定位在新版api中只支持https网站获取位置定位服务。

`ios10.x`后不再支持http协议下的geolocation获取位置服务，这给webapp开发者一个措手不及，但这只是开始，因为前面说了服务的源头，google的位置服务后续只支持https。

问题来了我们就去解决。需要https我们就用https就好了,但是一下子去申请https认证证书部署整个站点（通常多个域名）有点不切实际。我们可不可以在http下嵌入一个iframe之类的https页面，在用https的iframe去获取位置定位，在与当前窗口通信并把位置信息发过来呢。这事不用担心，很多第三方服务免费或付费的很快就会出现的。

#### 基本处理方法

```javascript
function successHandler(pos){
    // pos.lat
    // pos.lng
    console.log(pos);
}

function errorHandler(e){
    console.log(e);
}

function loadJS(url, cb) {
    var scriptEle = document.createElement("script");
    scriptEle.type = "text/javascript";
    scriptEle.src = url;
    scriptEle.onload = cb;
    document.body.appendChild(scriptEle);
}
var ua = navigator.userAgent;
var protocol = location.protocol;
```

#### H5定位代码 

原定位方式，后续只支持`https`，支付宝好像重写了`geolocation`,如果是支付宝授权商家`http`也可以直接用

```javascript

function originGeoLocation(){
    // 原定位方式，后续只支持https，支付宝好像重写了geolocation,如果是支付宝授权商家http也可以直接用
    window.navigator.geolocation.getCurrentPosition(function(result){
        successHandler({lat: result.coords.latitude, lng: result.coords.longitude});
    }, errorHandler, {
        enableHighAccuracy: true, maximumAge: 2000, timeout: 6000
    });
}

```

#### 支付宝

```javascript
// 支付宝
function aliGeolocation(){
    loadJS("https://as.alipayobjects.com/g/component/antbridge/1.1.1/antbridge.min.js",function(){
        if((Ali.alipayVersion).slice(0,3)>=8.1){
            Ali.geolocation.getCurrentPosition({
                timeout: 6000 //超时时间
            }, function(result) {
                if(result.errorCode){
                    //没有定位的情况
                    errorHandler(result);
                }else{
                    //成功定位的情况
                    //result.coords.latitude    double  纬度
                    //result.coords.longitude   double  经度
                    //result.city   string  城市
                    //result.province   string  省份
                    //result.cityCode   string  城市编码
                    //result.address    array   地址
                    successHandler({lat: result.coords.latitude, lng: result.coords.longitude});
                }
            });
        }else{
            originGeoLocation();
        }
    });
}
```

#### 微信、QQ、通用

```javascript
function qqGeolocation(){
    loadJS("//3gimg.qq.com/lightmap/components/geolocation/geolocation.min.js",function(){
        // "XXXXX-XXXXX-XXXXX-XXXXX-XXXXX-XXXXX" 你的腾讯地图开发者key http://lbs.qq.com/mykey.html
        var geolocation = new qq.maps.Geolocation("E6BZ-LQFW3-FQC3J-3XIXV-2FC32-L4BQY", "webapp");
        geolocation.getLocation(successHandler, errorHandler, {
            timeout: 6000, failTipFlag: true
        });
    }
}
```

#### 移动开发中适应各环境定位

```javascript
if(protocol === 'http:'){
    if(/AlipayClient/i.test(ua)){
        // 支付宝
        aliGeolocation();
    }else{
        // 除支付宝安卓，较为通用的
        qqGeolocation();
    }
} else {
    originGeoLocation();
}

```