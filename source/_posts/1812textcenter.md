---
title: 尝试解决移动端文字垂直居中的方案
date: 2018-12-25 19:46:46
tags: [css]
---
记录方案

背景：安卓IOS文字居中表现不一致，安卓似乎总是偏上一点点，不同机型有差别。

目前的方法可以实现居中，但只是表现一直，原因容我日后再看。

<!--more-->

## flexbox

用flexbox时候表现稍微好一些。
字体小于12px的时候用奇数。
【安卓IOS效果一致】

```
    display: flex;
    /*或者 display: inline-flex; */
    align-items: center;
```

## transform

整体写大一倍再`transform：scale(.5)`。
没有什么问题，但对周围元素布局有影响，适用范围有限。
