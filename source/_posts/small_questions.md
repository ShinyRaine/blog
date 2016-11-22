---
title: 收集一些面试常用题
date: 2016-09-2 15:59:50
tags: [javascript, css, html]
---
把一些各种地方看到的前端面试题，记不住的往这里放啦~
### 当代码 new foo(...) 执行时做了什么
1. 一个新对象被创建。它继承自foo.prototype，对象的__proto__指向构造函数的prototype。
2. 构造函数 foo 被执行。执行的时候，相应的传参会被传入，同时上下文(this)会被指定为这个新实例。new foo 等同于 new foo(), 只能用在不传递任何参数的情况。
3. 如果构造函数返回了一个“对象”，那么这个对象会取代整个new出来的结果。如果构造函数没有返回对象，那么new出来的结果为步骤1创建的对象，ps：一般情况下构造函数不返回任何值，不过用户如果想覆盖这个返回值，可以自己选择返回一个普通对象来覆盖。当然，返回数组也会覆盖，因为数组也是对象。
<!--more-->

### 发送一个url请求时做的事情
发送一个url请求时，浏览器开启一个线程来处理这个请求，同时在DNS服务器上启动一个DNS查询，来获得url对应的IP。接下来浏览器与服务器通过TCP三次握手来建立一个TCP/IP链接，握手包括一个同步报文，一个同步-应答报文，一个应答报文。首先由客户端建立通信，服务器应答接受客户端请求，最后由客户端发出请求已被接受的应答报文。一旦TCP/IP建立，浏览器发送一个HTTP GET请求。服务器找到资源使用HTTP相应返回资源，浏览器开始下载。浏览器会自上而下读取内容，渲染引擎绘制页面，JS引擎解析JS。

## Polyfill
polyfill 是“在旧版浏览器上复制标准 API 的 JavaScript 补充”,可以动态地加载 JavaScript 代码或库，在不支持这些标准 API 的浏览器中模拟它们。
例如，geolocation（地理位置）polyfill 可以在 navigator 对象上添加全局的 geolocation 对象，还能添加 getCurrentPosition 函数以及“坐标”回调对象，
所有这些都是 W3C 地理位置 API 定义的对象和函数。因为 polyfill 模拟标准 API，所以能够以一种面向所有浏览器未来的方式针对这些 API 进行开发，
一旦对这些 API 的支持变成绝对大多数，则可以方便地去掉 polyfill，无需做任何额外工作。

## 列举IE与其他浏览器不一样的特性
触发事件的元素被认为是目标（target）。而在 IE 中，目标包含在 event 对象的 srcElement 属性；

获取字符代码、如果按键代表一个字符（shift、ctrl、alt除外），IE 的 keyCode 会返回字符代码（Unicode），DOM 中按键的代码和字符是分离的，要获取字符代码，需要使用 charCode 属性；

阻止某个事件的默认行为，IE 中阻止某个事件的默认行为，必须将 returnValue 属性设置为 false，Mozilla 中，需要调用 preventDefault() 方法；

停止事件冒泡，IE 中阻止事件进一步冒泡，需要设置 cancelBubble 为 true，Mozzilla 中，需要调用 stopPropagation()
