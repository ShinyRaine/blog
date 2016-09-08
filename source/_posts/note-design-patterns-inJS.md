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
例子这里就不写了，知道组合对象很像上面那个宏就好了。

## 模板方法模式
模板方法模式基于继承，它包括两部分，一部分是抽象父类，一部分是具体实现的子类。在父类中封装了子类的算法框架，包括实现一些公共方法以及封装子类中所有方法的执行顺序。在JAVA语言中，父类是抽象类（abstract），不可生成实例，有一些抽象方法必须由子类去定义。父类中封装子类方法是如何去执行的方法叫做模板方法。
由于js中并没有抽象类，没有办法保证子类会重写父类中的抽象方法，需要进行检查保证该方法是由子类创建的。
在父类要变化的地方加上钩子（hook），钩子的返回结果决定了模板方法的步骤，这样程序就不再只适用于一种状况。
好莱坞原则：允许底层组件将自己挂钩到高层组件，高层组件会决定什么时候、以何种方式使用底层组件。发布-订阅模式和模板方法木事都是这个原则的体现。

```js
var Beverage = function() {}
Beverage.prototype.boil = function() {
	console.log('烧水')
}
Beverage.prototype.brew = function() {
	throw new Error('子类要重写brew')
}
Beverage.prototype.add = function() {
	throw new Error('子类要重写add')
}
Beverage.prototype.needAdd = function() { //这是个钩子
	return true
}
Beverage.prototype.init = function() {
	this.boil()
	this.brew()
	if (this.needAdd()) {
		this.add()
	}
}

var Coffee = function() {}
Coffee.prototype = new Beverage()
Coffee.prototype.brew = function() {
	console.log('冲咖啡')
}
Coffee.prototype.add = function() {
	console.log('加糖')
}

var Tea = function() {}
Tea.prototype = new Beverage()
Tea.prototype.brew = function() {
	console.log('泡茶')
}
Tea.prototype.add = function() {
	console.log('加柠檬')
}
Tea.prototype.needAdd = function() {
	return false
}

var coffee = new Coffee()
coffee.init()
var tea = new Tea()
tea.init()
```

其实我感觉后面那种不用继承来，而用闭包和参数传递实现的模板方法模式更js。

```js
var Beverage = function(param) {
	var boil = function() {
		console.log('烧水')
	}
	var brew = param.brew || function() {
		throw new Error('子类要重写brew')
	}
	var add = param.add || function() {
		throw new Error('子类要重写add')
	}
	var needAdd = param.needAdd || function() { //这是个钩子
		return true
	}

	var F = function(){}
	F.prototype.init = function() {
		boil()
		brew()
		if (needAdd()) {
			add()
		}
	}
	return F
}


var Coffee = Beverage({
	brew : function() {
	console.log('冲咖啡')
	},
	add : function() {
		console.log('加糖')
	}
})

var Tea = Beverage({
	brew : function() {
		console.log('泡茶')
	},
	add : function() {
		console.log('加柠檬')
	},
	needAdd : function() {
		return false
	}	
}) 


var coffee = new Coffee()
coffee.init()
var tea = new Tea()
tea.init()
```
## 享元模式
享元模式是用共享技术来有效支持大细粒度的对象。用来优化要创建大量类似对象的情况。
享元模式将对象的属性划分为内部状态和外部状态。内部状态储存在对象内部，独立于具体场景，通常不会变化，可以被一切对象共享；外部状态取决于具体场景，不能被共享。把相同的内部状态指定为一个共享对象，外部状态在必要时传入组成完整的对象。组装外部状态的过程会花费一定的时间，享元模式是用时间来换取空间的模式。

```js
var Upload = function(type) {
	this.type = type //一个内部状态
}
Upload.prototype.delFile = function(id) {
	uploadManager.setExternalState(id, this)
	if (this.fileSize < 2000) {
		return this.dom.parentNode.removeChild(this.dom)
	}
	if (window.confirm('delete?'+this,fileName)) {
		return this.dom.parentNode.removeChild(this.dom)
	}
}
// 实例化的工厂
var uploadFactory = (function() {
	var createFlyWeightObjs = {}
	return {
		create: function(type) {
			if (createFlyWeightObjs[type]) {
				return createFlyWeightObjs[type]
			}else {
				return createFlyWeightObjs[type] = new Upload(type)
			}
		}
	}
})()
// 管理器封装外部状态
var uploadManager = (function() {
	var uploadDatabase = {}
	return {
		add: function(id, type, fileName, fileSize) {
			var flyWeightObj = uploadFactory.create(type)

			var dom = document.createElement('div')
			dom.innerHTML = 'name:' + fileName + ';size:' + fileSize + '<button class="delete">delete</button>'
			dom.querySelector('.delete').onclick = function() {
				flyWeightObj.delFile(id)
			}
			document.body.appendChild(dom)

			uploadDatabase[id] = {
				fileName: fileName,
				fileSize: fileSize,
				dom: dom
			}
			return flyWeightObj
		},
		setExternalState: function(id, flyWeightObj) {
			var uploadData = uploadDatabase[id]
			for (var i in uploadData) {
				flyWeightObj[i] = uploadData[i]
			}
		}
	}
})()
var id = 0
var startUpload = function(type, files) {
	for (var i = 0; i < files.length; i++) {
		var uploadObj = uploadManager.add(++id, type, files[i].fileName, files[i].fileSize)
	}
}
startUpload('plugin', [
	{
	fileName: '1',
	fileSize: 1000
	},
	{
	fileName: '2',
	fileSize: 1000
	},
	{
	fileName: '3',
	fileSize: 1000
	}
])
```
<留坑>