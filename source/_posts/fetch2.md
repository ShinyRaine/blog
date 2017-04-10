---
title: fetch again
date: 2017-03-24 16:20:58
tags: [javascript]
---
写上一篇fetch介绍文章的时候，我只粗略地翻了一下API和几篇博文，做的东西也只是简单的数据交互，以致于对它的理解并不全面，导致现在踩坑。
如果之后还踩坑的话会继续记录在这篇博文中。
<!--more-->
## fetch标准
fetch标准包括下面几个过程：
1.
### 没有cookie 没有会话
fetch默认是无状态的。
> 默认, fetch 将不会发送或接收任何 cookies 从服务器, 如果站点依赖于维护一个用户会话，则导致未经认证的请求，要发送 cookies，必须发送凭据头（credentials）。

在学习使用express-session的时候，我使用了fetch发送请求。发现sessionID被记录在cookies中，但是在后台访问req.session仍然是undefined。
如果需要状态，需要设置credentials为same-origin（在同源情况下发送cookies）或者include（所有情况）
```js
fetch('/user/login', {
  method: 'POST',
  headers: {'Content-Type': 'application/json'},
  credentials: 'same-origin',
  body: JSON.stringify({name: 'xxxx', password: 'xxxx'})
})
```
