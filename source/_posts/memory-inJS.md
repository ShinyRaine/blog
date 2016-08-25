---
title: JS中的内存问题
date: 2016-07-26 10:06:50
tags: [javascript]
---
## 数据储存
JS中对象分为基本类型（undefined,null,boolean,number,string）和引用类型（object,array,function），基本类型存放在栈内存，引用类型存放在堆内存。其中引用类型数据在栈中储存了指针。
## JS的垃圾回收
JS有自动垃圾回收机制，执行环境会负责管理代码执行过程中使用的内存。原理：找出不再使用的变量，释放其内存。垃圾回收器会按照固定的时间间隔周期性的执行该操作。
局部变量只在函数执行的过程中存在。在这个过程中，为其在栈或堆上分配相应的空间，然后在函数中使用这些变量，直到函数结束。这时，垃圾收集器要跟踪变量，判断其是否有用，对不再有用的变量打上标记，以便回收内存。对于标识无用变量，通常有以下两种策略。
<!--more-->
### 标记清除
垃圾收集器运行时将所有内存中的变量打上标记，然后去掉环境中变量及被其引用的变量的标记，将带标记的变量视为准备删除。最后销毁带标记变量回收其内存空间，完成清楚内存工作。
### 引用计数
记录每个值被引用的次数，当这个值的引用次数变为0时回收其内存空间。
这种方法会遇到循环引用问题。循环引用指A中包含指向B的指针，B中包含指向A的指针，在引用计数方法中，他们的引用次数永远都不会是0。
在低版本IE（IE9以下）中BOM和DOM是用C++以COM的形式实现的，COM使用了这种垃圾回收策略。
## 内存泄漏
内存泄漏也称作“存储渗漏”，用动态存储分配函数动态开辟的空间，在使用完毕后未释放，结果导致一直占据该内存单元。直到程序结束。（其实说白了就是该内存空间使用完毕之后未回收）即所谓内存泄漏。
### 意外的全局变量
就是函数里不加声明的新变量
### 一些闭包使用
如果一个变量被任一个闭包使用了，它会在所有的闭包词法环境结束之后才被释放，会导致内存泄漏。
例子:

```js
var run = function () {
  var str = new Array(1000000).join('*');
  var doSomethingWithStr = function () {
    if (str === 'something')
      console.log("str was something");
  };
  // doSomethingWithStr();
  var logIt = function () {
    console.log('interval');
  }
  // logIt()
  return logIt
};
var runStr = run()
runStr()
```
在上面的代码中，会因为str产生1M的内存，而浏览器不会自动回收这部分内存。因为外部的变量runStr始终引用了闭包作用域中的变量。
下面这段代码每秒都会产生1M内存。你可以在这篇文章里找到这段代码：[一个意想不到的Javascript内存泄漏](http://www.cnblogs.com/friskfly/p/3171834.html)

```js
var run = function () {
  var str = new Array(1000000).join('*');
  var doSomethingWithStr = function () {
    if (str === 'something')
      console.log("str was something");
  };
  var logIt = function () {
    console.log('interval');
  }
  setInterval(logIt, 100);
};
setInterval(run, 1000);
```
### 低版本IE
低版本IE中对DOM/BOM元素进行循环引用会导致内存泄漏，需要将引用赋值为null。如果闭包的作用于链中存在DOM/BOM元素，该元素也永远不会被销毁，需要将其赋值为null。

