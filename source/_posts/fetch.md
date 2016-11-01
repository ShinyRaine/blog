---
title: fetch
date: 2016-04-27 16:20:58
tags: [javascript]
---
最近赶时髦用了fetch，下面就来简单记录一下用法吧。

## 一个fetch请求
```javascript
fetch('data.json').then(function(response) {
  if(response.ok) {
    response.json().then(function(data) {
      console.log(data)
    });
  } else {
    console.log('Network response was not ok.');
  }
})
.catch(function(error) {
  console.log('There has been a problem with your fetch operation: ' + error.message);
});
```
<!--more-->
## Headers
headers对应了请求的头部信息。
Fetch API 的Headers类允许你去对HTTP request和response headers执行各种操作。这些操作包括：检索, 设置, 添加和删除。
```javascript
var reqHeaders = new Headers();
reqHeaders.append("Content-Type", "text/plain"
reqHeaders.append("Content-Length", content.length.toString());
reqHeaders.append("X-Custom-Header", "ProcessThisImmediately");
```
或者
```javascript
reqHeaders = new Headers({
  "Content-Type": "text/plain",
  "Content-Length": content.length.toString(),
  "X-Custom-Header": "ProcessThisImmediately",
});
```
提供的接口可以让我们对其进行操作
```javascript
reqHeaders.append() //添加
reqHeaders.delete() //删除
reqHeaders.get() //查询，返回第一个对应属性的值
reqHeaders.getAll() //查询，返回所有值组成的数组
reqHeaders.has() //查询，返回表示是否有对应属性的布尔值
```

## Request
Request接口定义了通过HTTP请求资源的request格式。参数需要URL、method和headers，同时Request也接受一个特定的body，mode，credentials以及cache hints。

```javascript
var uploadReq = new Request("/uploadImage", {
  method: "POST",
  headers: {
    "Content-Type": "image/png",
  },
  body: "image data"，
  mode: "cors"
});
```
mode属性用来决定是否允许跨域请求，以及哪些response属性可读。可选的mode属性值为same-origin，no-cors（默认）以及cors。
same-origin，如果一个请求是跨域的，那么返回一个简单的error.
no-cors，允许来自CDN的脚本、其他域的图片和其他一些跨域资源，但是首先有个前提条件，就是请求的method只能是"HEAD","GET"或者"POST"。任何 ServiceWorkers 拦截了这些请求，它不能随意添加或者改写任何headers。JavaScript不能访问Response中的任何属性。
cors，可以跨域请求来从第三方提供的API获取数据。这个模式遵守CORS协议。只有有限的一些headers被暴露给Response对象，但是body是可读的。

## Response
Response实例通常在fetch()的回调中获得。
Response中最常见的属性是status和statusText，对应HTTP请求的status和reason。还有一个"ok"属性，当status为2xx的时候它是true。通过这些属性来判断请求是否成功。

body可以是一下几种类型。
- ArrayBuffer
- ArrayBufferView(Uint8Array and friends)
- Blob/File
- 字符串
- URLSearchParams
- FormData

Response提供了几个方法来处理它的body，这些方法都返回一个Promise对象。
- arrayBuffer()
- blob()
- json()
- text()
- formData()

## 流和克隆
Request 和 Response 的 body 只能被读取一次。这样设计的目的是为了之后兼容基于流的 API，我们的目的是当数据到达时就进行相应的处理，这样就使得 JavaScript 可以处理大文件例如视频，并且可以支持实时压缩和编辑。
API 为这两个对象提供了一个 clone() 方法。调用这个方法可以得到一个克隆对象，对象中包含全新的 body。clone() 必须要在使用 body 之前调用，也就是先 clone() 再读使用。
```javascript
addEventListener('fetch', function(evt) {
  var sheep = new Response("Dolly");
  console.log(sheep.bodyUsed); // false
  var clone = sheep.clone();
  console.log(clone.bodyUsed); // false

  clone.text();
  console.log(sheep.bodyUsed); // false
  console.log(clone.bodyUsed); // true

  evt.respondWith(cache.add(sheep.clone()).then(function(e) {
    return sheep;
  });
});
```
