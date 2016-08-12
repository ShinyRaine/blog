---
title: Web Workers
date: 2016-08-05 19:36:36
tags: [javascript]
---
Web Workers 可以让JS在后台运行，避免长时间进程阻塞控制交互的进程。
worker要在服务器环境中执行。
[参考文档](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers)
## Worker对象
通过new创建一个Worker，传入路径，浏览器下载对应的JS；使用postMessage传递信息；使用message事件检测返回数据，返回的数据在event.data中；使用error时间监听错误，错误的文件名、代码行数、错误信息分别在event的filename、lineno、message中；使用termiate()方法终止工作。
<!--more-->
```js
var worker = new Worker("xxx.js") 
worker.postMessage({
	type:"cmd",
	message:"start"
	})

worker.onmessage = function(event) {
	var data = event.data
}
worker.onerror = function(event) {
	console.log(event.filename,event.lineno,event.message)
}

worker.terminate()
```
## 使用
Worker中执行的代码在另一个作用域中。Worker中代码不能访问DOM。
在Worker内部，this、self引用的是全局worker对象。
当外部调用postMessage()时，数据以异步的方式传到worker，触发worker中的message事件。内部处理完之后，调用postMessage()方法把数据传回页面，触发页面中worker的message事件。
在worker内部，使用close()停止工作；使用importScripts()引入脚本，这个方法传入url，按传入的先后顺序试行脚本。

```js
importScripts('a.js','b.js')
this.onmessage = function(event) {
	var data = event.data
	//数据处理
	self.postMessage(data)
}
```
## Shared workers
可以跨窗口，跨iframes，跨workers。
外部接口：
```js
var myWorker = new SharedWorker("worker.js");
myWorker.port.start();

myWorker.port.postMessage();
```
worker内部：
```js
onconnect = function(e) {
  var port = e.ports[0];

  port.onmessage = function(e) {
    var workerResult = e.data
    port.postMessage(workerResult);
  }

}
```
案例[simple-shared-worker](https://github.com/mdn/simple-shared-worker)