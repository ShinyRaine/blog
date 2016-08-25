---
title: 使用rem单位时css sprites的坑
date: 2016-05-13 16:48:34
tags: [css]
---
在使用rem单位时，用雪碧图（我不喜欢这个名字！）做背景图片在移动端有时会有错位。pc模拟是没有问题的，而且错位的情况不同的手机还不太一样。这里说几个解决的办法。
出现这个问题的原因我找了很久都找不到，如果有知道的大神请联系我！

### background-size和background-position都用rem
我实际使用中是用这个方法解决问题的。但是据说这样还是会有错位的时候。

### 转换为px
这个是一定有效的方法。background-size 和 position都用px写死。然后动态增加.zoom{transform:scale(xxx)}样式。把使用到雪碧图的元素用zoom缩放。（来自淘宝ued博客的方法）

<!--more-->
在尝试解决这个问题的时候，我发现background-position的定位方式非常奇怪，顺便也记录在这里。
### px,em单位
图片的左上角开始计算与容器左上角的距离。
### 百分比定位
如果是m% n%，那么就把图片的(m%,n%)的坐标点，放置在容器的(m%,n%)的位置。

## rem布局代码
```js
(function (doc, win) {
    var docEl = doc.documentElement,
        resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
        recalc = function () {
            var clientWidth = docEl.clientWidth;
            if (!clientWidth) return;
            if(clientWidth>=640){
                docEl.style.fontSize = '100px';
            }else{
                docEl.style.fontSize = 100 * (clientWidth / 640) + 'px';
            }
        };

    if (!doc.addEventListener) return;
    win.addEventListener(resizeEvt, recalc, false);
    doc.addEventListener('DOMContentLoaded', recalc, false);
})(document, window);
```
