---
title: 尝试解决移动端文字垂直居中的方案
date: 2018-12-25 19:46:46
tags: [css]
---
记录方案

背景：安卓IOS文字居中表现不一致，安卓似乎总是偏上一点点，不同机型有差别。

目前的方法无法保证所有机型居中，发现是个无解的问题。

<!--more-->

## flexbox

用flexbox时候表现稍微好一些。目前我们采取了这种方案，即使不能让所有机型居中，这种是表现最好的一种了。

- line-height: normal 部分机型有用
- <html lang="zh-cmn-Hans"> + font-family设置中文字体 部分机型有用

```
    display: flex;
    /*或者 display: inline-flex; */
    align-items: center;
    line-height: normal;

```

## transform

整体写大一倍再`transform：scale(.5)`。
没有什么问题，但对周围元素布局有影响，适用范围有限。

## 安卓无法居中原因

此问题产生的原因是不同机型中字体的基线位置不同，导致无法表现一致。
