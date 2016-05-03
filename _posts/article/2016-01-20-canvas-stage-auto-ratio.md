---
layout: post
category : [Solution]
title : "canvas动画的一些适配技巧"
tagline: "方案"
tags : [solution, canvas, ratio, 自适应, 抗锯齿, 动画]
excerpt: canvas画布作画时我们需要对多种像素密度的屏幕做适配，否者可能出现锯齿而影响视觉效果，这里提供一个使用canvas绘图的优化和适配方法
---
{% include JB/setup %}

canvas画布作画时我们需要对多种像素密度的屏幕做适配，否者可能出现锯齿而影响视觉效果，这里提供一些在使用canvas绘图的优化和适配方法

### 当我们在创建一个canvas动画或游戏(交互动画)的时候我们可以考虑优化canvas的重绘模块

```html
<div id="stage" class="stage">
	<!-- 背景层，一般不会改变的背景图等，基本不更新 -->
	<canvas class="layer-bg"></canvas>
	<!-- 动画层，一般场景变化较快，更新频繁 -->
	<canvas class="layer-ani"></canvas>
	<!-- ui层，一般用户操作交互的一些文案，刷新极少 -->
	<canvas class="layer-ui"></canvas>
</div>
```

```scss
.state {
	position: relative;
	background-color: #fff;
	
	// 保证画布局中，更根据需要定位每一层的位置
	canvas {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		margin: auto;
	}
}
```

**抗锯齿并不会影响绘画的体验**

```js
	// 画布的大小
	var stageH = 400, stageW = 360;
	var stageEle = document.getElementById('stage');

	var bgEle = stageEle.querySelector('.layer-bg');
	var aniEle = stageEle.querySelector('.layer-ani');
	var uiEle = stageEle.querySelector('.layer-ui');

	// 这里有两种理解：
	// 一种是获得画布绘制的域，context以画布为操作对象，就像
	// 缝纫机一样，针固定，人操控布旋转移动然后绘出不同的线路或图案。
	// 第二种是获得绘画的笔，pen很神奇可以配置各种参数，就像唐伯虎在
	// 画一幅巨画，画布固定，人动笔围着画布旋转移动完成绘画。
	bpen = bgEle.getContext('2d');
	gpen = aniEle.getContext('2d');
	upen = uiEle.getContext('2d');

	// 获取像素密度处理锯齿问题
	var ratio = window.devicePixelRatio || 2;
	// 画布dom盒子 当然可以更具需要设置不同大小
	uiEle.style.height = aniEle.style.height = bgEle.style.height = stageH + 'px';
	uiEle.style.width = aniEle.style.width = bgEle.style.width = stageW + 'px';
	// 画布实际大小 ratio * dom.style.height/width
	uiEle.height = aniEle.height = bgEle.height = stageH * ratio;
	uiEle.width = aniEle.width = bgEle.width = stageW * ratio;
	// 等比放缩
	bpen.scale(ratio, ratio); gpen.scale(ratio, ratio); upen.scale(ratio, ratio);
	
	// 然后以正常的stageH为高, stageW为宽的大小去绘画。不用理会当前的是几倍图画布

```

### 活用Demo

<iframe height='360' scrolling='no' src='//codepen.io/shalles/embed/zrJEyL/?height=360&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='http://codepen.io/shalles/pen/zrJEyL/'>zrJEyL</a> by shalles (<a href='http://codepen.io/shalles'>@shalles</a>) on <a href='http://codepen.io'>CodePen</a>.
</iframe>

