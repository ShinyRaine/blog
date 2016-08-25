---
title: 笔记-JS中的设计模式
date: 2016-08-15 11:03:35
tags: [javascript]
---
JavaScript设计模式与开发实践 + JavaScript设计模式（动物的） 的笔记

## 单例模式 singleton
- 保证一个类仅有一个实例，并且提供一个访问它的全局属性。
从单例模式的特点出发，在js中的全局变量就可以作为单例来使用。使用太多全局变量不是很好，于是可以采用命名空间、闭包封装私有变量的方式来优化这个使用。

### 惰性单例
- 在需要的时候创建对象的单例
如下函数返回一个可以创建唯一实例的函数：
```js
var getSingle = function (fn) {
	var result
	return function () {
		return result || (result = fn.apply(this,arguments))
	}
}
```
<!--more-->

## 策略模式
定义一系列算法，把它们一个个封装起来，并且使它们可以互相替换。
将算法的使用和实现分开。
一个简单的例子。
```js
var strategis = {
	s : function (s) {
		return s * 4
	},
	a : function (s) {
		return s * 3
	}
}
var calculate = function (l,s) {
	return strategis[l](s)
}
console.log(calculate('s',200))
```

## 代理模式
为对象提供一个代用品或占位符，以便控制对它的访问。
保护代理可以拒绝一部分请求，用于控制不同权限的对象对目标对象的访问；虚拟代理延迟创建开销大的对象；
虚拟代理的例子：
```js
var myImage = (function () {
	var imgNode = document.createElement('img')
	document.body.appendChild(imgNode)

	return function (src) {
		imgNode.src = src
	}
})()
var proxyImage = (function () {
	var img = new Image
	img.onload = function () {
		myImage(this.src)
	}
	return function (src) {
		myImage('loading.gif')
		img.src = src
	}
})()
```
缓存代理可以为一些开销大的运算结果提供暂时储存，下次运行时返回储存的结果。
例子：
```js
var mult = function () {
	var a = 1
	for (var i = 0; i < arguments.length; i++) {
		a = a * arguments[i]
	}
	return a
}
var proxyMult = (function() {
	var cache = {}
	return function() {
		var args = Array.prototype.join.call(arguments,',')
		if (args in cache) {
			return cache[args]
		}
		return cache[args] = mult.apply(this, arguments)
	}
})()
```
## 迭代器模式
迭代器模式指提供一种方法顺序访问一个聚合对象中的各个元素，不暴露对象的内部表示。
JS中有很多内置的迭代器，比如Array.prototype.forEach、Array.prototype.map等。

## 发布-订阅模式

<明天继续>