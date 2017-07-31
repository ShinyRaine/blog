---
title: 关于addEventListener
date: 2017-07-18 10:30:00
tags: [javascript]
---
## 语法

```js
target.addEventListener(type, listener[, options]);
target.addEventListener(type, listener[, useCapture]);
```
第三个参数是可选的，可以是对象也可以是布尔值。

### options配置项如下：
- capture:Boolean，表示 listener 会在该类型的事件捕获阶段传播到该 EventTarget 时触发。
- once:Boolean，表示 listener 在添加之后最多只调用一次。如果是 true， listener 会在其被调用之后自动移除。
- passive:Boolean，表示 listener 永远不会调用 preventDefault()。如果 listener 仍然调用了这个函数，客户端将会忽略它并抛出一个控制台警告。

配置项passive是true的时候表示函数不会阻止默认事件。这是针对移动端的滚屏优化，提前告知浏览器函数是否阻止默认事件，而不必等监听器执行完毕后再决定是否触发默认行为。
<!-- more -->
## 一个参考链接

https://segmentfault.com/a/1190000007913386
