---
title: 笔记-JS中的设计模式
date: 2016-08-15 11:03:35
tags: [javascript]
---
JavaScript设计模式与开发实践 + JavaScript设计模式（动物的）+ headfirst设计模式 的笔记

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
它定义对象间一对多的依赖关系，当一个对象状态改变时，所有依赖于它的对象都将得到通知。
实际上，我们平时编写的事件监听就可以说是一种发布-订阅模式。可以把DOM元素看成一个订阅者，把事件的触发看成发布。拿这种模式写起代码来，也总有一种这是事件的熟悉感。

```js
var event = (function(){
	var clientList = {}

	var listen = function(key, fn) {
		if (!clientList[key]) {
			clientList[key] = []
		}
		clientList[key].push( fn )
	}

	var trigger = function() {
		var key = Array.prototype.shift.call(arguments)
		var fns = clientList[key]
		if (fns.length === 0 && !fns) {
			return false
		}
		for (var i = 0; i < fns.length; i++) {
			fns[i].apply(this,arguments)
		}
	}

	var remove = function(key, fn) {
		var fns = clientList[key]
		if (fns.length === 0 && !fns) {
			return false
		}
		if (!fn) {
			fns = []
		}else {
			for (var i = 0; i < fns.length; i++) {
				if (fns[i] == fn) {
					fns.splice(i,1)
				} 
			}
		}
	}

	return {
		listen:listen,
		trigger:trigger,
		remove:remove
	}
})()

event.listen('my',function(num) {
	console.log(num)
})
event.trigger('my',7)
```
一些书和文章中把观察者模式和发布-订阅模式算作一个，而另一本书中单独讲了这两种模式，在实现上有微小差别。个人感觉思路不变还是算成一种的比较好吧。

## 命令模式
在一些对象向另一些对象发送请求时，通过命令模式可以将请求的发送者和接收者分离开来，或者说消除它们的耦合关系。
在JS当中，使用回调函数可以说是命令模式的体现。我们完全可以把发送的请求函数作为参数传递，比如下面这样的代码。
```js
var receiver1 = {
	log: function() {
		console.log(1)
	}
}
var setCommand = function(button, func) {
	button.onclick = func.bind(this)
}
var LogCommand = function(receiver) {
	return function() {
		receiver.log()
	}
}
var logCommad = new LogCommand(receiver1)
setCommand(button1, logCommad) //假设有个叫button1的按钮
```

上面只是实现了能让一个命令的执行的命令模式。除此以外，命令模式需要可以进行撤销、重做、一次执行多个命令的功能。这样的话我们让command对象返回出execude方法（执行）、unexecude方法（撤销），添加一个堆栈来保存执行过的命令，使用一个大的对象（宏）来保存需要执行的命令。下面贴出一些例子。

```js
var receiver1 = {
	log: function() {
		console.log('on')
	},
	unlog: function() {
		console.log('off') //假装有撤销的代码
	}
}
var setCommand = function(button, cmd) {
	button.onclick = cmd.execude
}
var offCommand = function(button, cmd) {
	button.onclick = cmd.unexecude
}
var LogCommand = function(receiver) {
	return {
		execude: function() {
			receiver.log()
		},
		unexecude: function() {
			receiver.unlog()
		}
	}
}
var logCommad = new LogCommand(receiver1)
setCommand(button1, logCommad) //假设有个叫button1的按钮
offCommand(button2, logCommad) //假设有个叫button2的按钮
```
一次执行多个命令:

```js
var BigCommand = function() {
	return {
		commands: [],
		add: function(command) {
			this.commands.push(command)
		},
		execute: function() {
			for(var i = 0;i < commands.length; i++){
				commands[i].execute()
			}
		}
	}
}
```
## 组合模式
组合模式将对象组合生成树形结构，以表示“部分-整体”的层次结构。除了用来表示树形结构外，组合模式可以通过对象的多态性表现，使用户对单个对象和组合对象的使用具有一致性。用户请求从树的顶端对象向下传递，到达相应节点后进行相应的处理，这样，用户只需要关心顶层对象，像是上面的宏，叶对象只要都拥有叫做execute方法就可以了。
组合模式的组合对象和叶对象是聚合关系（HAS-A），不是父子关系。组合对象和叶对象需要有相同的接口和操作的一致性。

<留坑>