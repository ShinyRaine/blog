---
title: web数据储存
date: 2016-07-31 16:03:11
tags: [html,javascript]
---
## 离线应用
可以用navigator.onLine属性检测设备是否可以访问网络。window上的两个事件：online和offline，标志着网络状态的改变。
### 离线缓存
可以使用一个描述文件在浏览器的缓存区保存资源，使应用可以离线使用。
``` html
<html manifest="/a.manifest">
```
文件后缀可以用manifest和appcache
示例文件：
<!--more-->
```
CACHE MANIFEST
js.js
style.css

NETWORK: //需要在线访问的资源
network.html

FALLBACK: //后备资源,当尝试请求资源失败时会使用后备记录
fallback.html
```
js中可以用applicationCache对象可以检测、控制缓存行为。
[相关MDN文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Using_the_application_cache)
## cookie
服务器对任意HTTP请求发送Set-Cookie HTTP头作为相应的一部分。浏览器储存这样的信息，之后通过为每个请求添加Cookie HTTP头将信息返回服务器。
每个域的cookie总数有限，总大小4kB。
包含的信息：名称、值、域(domain)、路径(path)、失效时间(expires)、安全标志(secure)。
### JS中的处理
document.cookie返回所有可用cookie字符串。需要使用decodeURIComponent()解码。
获取
```js
var getCookie = function(name){
	var cookie = document.cookie
	var cookieName = encodeURIComponent(name),
		start = cookie.indexOf(cookieName),
		value

	if (start > -1) {
		start += cookieName.length + 1
		var end = cookie.indexOf(";",start)
		if(end == -1){
			end = cookie.length
		}
		value = decodeURIComponent(cookie.slice(start,end))
	}
	return value
}
```
添加
```js
var setCookie = function(name,value,expires,path,domain,secure){
	var text = encodeURIComponent(name) + "=" + encodeURIComponent(value)
	if(expires instanceof Date){
		text += ";" + "expires=" + exprires.toGMTString()
	}
	if(path) {
		text += ";" + "path=" + path
	}
	if(domain) {
		text += ";" + "domain=" + domain
	}
	if(secure) {
		text += ";" + "secure"
	}
	document.cookie = text
}
````
## WebStorage
WebStorage提供在cookie以外的储存数据途径，可以储存大量数据。
[相关文档](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)
### sessionStorage
储存特定于某个会话的数据。数据保存到浏览器关闭。适用于针对会话的小数据存储。
```js
// 保存数据到sessionStorage
sessionStorage.setItem('key', 'value');
sessionStorage.key = 'value'
// 从sessionStorage获取数据
var data = sessionStorage.getItem('key');
var data = sessionStorage.data
//删除
sessionStorage.removeItem('key')
```
### localStorage
数据一直保存。API与sessionStorage一致，它们都是Storage的实例。
## IndexDB
IndexedDB是用对象保存数据的异步数据库。
[相关文档](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)
由于兼容问题，将这样使用indexDB:
```js
var indexedDB = window.indexedDB || window.mozIndexedDB || window.webkitIndexedDB || window.msIndexedDB;
```
### 创建
用open方法打开数据库，如果名称已存在，就打开它，否则创建并打开。返回一个IDBRequest对象。

```js
var request = indexedDB.open("MyTestDatabase")
const data = [
  { num: "1", name: "Bill", age: 35 },
  { num: "2", name: "Donna", age: 32 }
];
request.onerror = function(event) {
  console.log("request error:" + event.target.errorCode)
};
request.onupgradeneeded = function(event) {
  var database = event.target.result;

  // 创建一个对象存储空间
  // 我们将使用 "num" 作为key
  var objectStore = database.createObjectStore("mydata", { keyPath: "num" });

  // 通过add()或put()向数据空间添加数据
  for (var i in data) {
    objectStore.add(data[i]);
  }

};
```
### 基本操作
transaction() 返回一个事务对象。第一个参数是事务希望跨越的对象存储空间的列表。传入一个空数组可以让事务能够跨越所有的对象存储空间。如果没有指定第二个参数，得到的是只读事务。
取得事务后可以使用objectStore()访问储存空间，用add()或put()添加，用get()取值，用delete()进行删除，clear()清空所有对象。

```js
//创建事务，访问mydata空间，修改访问方式为readwrite
var transaction = database.transaction("mydata", "readwrite")

var rq = transaction.objectStore("mydata").get("1")
rq.onerror = function(event) {
  console.log("request error:" + event.target.errorCode)
};
rq.onsuccess = function(event){
	console.log(event.target.result)
}
```

事务和事件循环的联系非常密切。如果创建了一个事务但是并没有使用它就返回给事件循环，那么事务将变得无效。保持事务活跃的唯一方法就是在其上构建一个请求。当请求完成时得到一个 DOM 事件，假设请求成功了，将会有另外一个机会在回调中来延长这个事务。如果你没有延长事务就返回到了事件循环，那么事务将会变得不活跃，依此类推。只要还有待处理的请求事务就会保持活跃。
事务可以接收三种不同类型的 DOM 事件： error，abort，以及 complete。
### 游标
遍历存储空间，检索多个对象。store对象上调用openCursor()创建游标，返回一个请求对象。
默认每个游标只发一个请求，需要发另一个请求时使用continue(key)移动到指定key，或向前移动advance(count)

```js
var objectStore = database.transaction("mydata").objectStore("mydata");

objectStore.openCursor().onsuccess = function(event) {
  var cursor = event.target.result,
  	  value
  if (cursor) {
    console.log(cursor)
    if(cursor.num == "1"){
    	value = cursor.value
    	value.name = "abc" //更新信息
    	updateRq = cursor.update()
    	updateRq.onsuccess = function(){

    	}
    	updateRq.onerror = function(){
    		
    	}
    }
    if(cursor.num == "2"){
    	deleteRq = cursor.delete() //删除
    	deleteRq.onsuccess = function(){

    	}
    	deleteRq.onerror = function(){
    		
    	}
    }
    cursor.continue()
  }
  else {
    console.log("No more entries!");
  }
};
```
### 索引
```js
var index = objectStore.index("name");
index.get("Donna").onsuccess = function(event) {
  alert("Donna's key is " + event.target.result.key);
};
```
在索引上调用openCursor()也可以创建游标。
 
 （有点虎头蛇尾的一篇，毕竟没有太深入研究数据库。使用请参考MDN文档等。）