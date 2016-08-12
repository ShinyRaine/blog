---
title: visibility API
date: 2016-08-05 10:27:35
tags: [javascript]
---
这是一篇简短的小博文，介绍检测页面是否可见的API。
参考 https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API

document.hidden：如果页面可见返回true，否则返回false

document.visibilityState：返回状态字符串，可见visible，不可见hidden。文档上还写了prerender和unloaded。
<!--more-->
visibilitychange事件在页面可见性变化时触发。切换标签、最小化窗口会触发，但是如果打开网页状态下开启其他应用不会触发。
下面是兼容代码：

```js
var hidden,visibilityChange
if (typeof document.hidden !== "undefined") {
  hidden = "hidden";
  visibilityChange = "visibilitychange";
} else if (typeof document.mozHidden !== "undefined") {
  hidden = "mozHidden";
  visibilityChange = "mozvisibilitychange";
} else if (typeof document.msHidden !== "undefined") {
  hidden = "msHidden";
  visibilityChange = "msvisibilitychange";
} else if (typeof document.webkitHidden !== "undefined") {
  hidden = "webkitHidden";
  visibilityChange = "webkitvisibilitychange";
}

function handleVisibilityChange() {
	console.log(document.visibilityState)
	if (document[hidden]) {
		hiddenFun() //这里是要执行的函数
	}else {
		visibleFun() //这里是要执行的函数
	}
}
document.addEventListener(visibilityChange, handleVisibilityChange, false);
```
写的一个小demo：
https://github.com/RizzleCi/mydemo/tree/master/visibilityAPI
