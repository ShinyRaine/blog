---
title: JS中的类
date: 2016-10-31 19:37:23
tags: [javascript]
---

用了那么久的class关键字，都没想过好好看看相关的内容。

## 声明
es6中引入了class关键字，这是一个语法糖，方便我们处理对象的创建/继承。
可以把类看做是一个构造函数。

定义类可以用下面两种方式。
```js
class Person {
  constructor() {

  }
  say(){

  }
}
```
<!--more-->
```js
const Person = class {
  constructor() {

  }
  say(){

  }
}
```
- 类的定义不会有变量提升现象。如果类的定义在使用之后会抛出错误。
- 类不可以被重复定义，但是可以改写。 // 不知道这是不是规范，测试结果火狐和谷歌浏览器都是这种表现
- 类的构造函数，只能使用new关键字调用，否则会报错。

## constructor
constructor是一个用来创建和初始化的对象的特殊方法。一个类中只允许有一个constructor。如果在定义的时候没有写constructor方法，会默认添加空的constructor。
```js
class Person {
  constructor(name) {
    this.name = name
  }
  say(){
    console.log("hi, it's "+ this.name)
  }
}
let cxy = new Person('cxy')  // Person {name: "cxy"}
cxy.say() // hi, it's cxy
```
在使用new关键字调用类时，会默认调用constructor方法。
在子类中使用super()可以调用父类的构造器方法。

## 方法
在类中定义的方法相当于定义在原型上面。

### 使用get/set关键字来定义方法。
get关键字定义的方法返回特定值。set关键字使方法通过=号传递参数。
```js
class Person {
  constructor(name) {
    this.name = name
    this.words = ''
  }
  get getwords() {
    return this.words
  }
  set input(value) {
    this.words = value
  }
}
let cxy = new Person('cxy')  
console.log(cxy.getwords )  // ''
cxy.input = 'hello'
console.log(cxy.getwords) // 'hello'
```

### 定义generator方法
在方法前面加上`*`号让它成为一个generator方法。
```js
class Obj {
  constructor() {
    this.value = 0
  }
  * count() {
    while(true)
      yield this.value++;
  }
}

let it = new Obj()
console.log(it.value); // 0
it.count().next()
console.log(it.value); // 1
```

### 使用static定义静态方法
静态方法是不需要对类进行实例化，使用类名就可以直接访问的方法。
静态方法不能被实例调用。
静态方法会被继承。
```js
class Obj {
  constructor() {
  }
  static foo() {
    console.log('bar')
  }
}
let a = new Obj()
Obj.foo() // 'bar'
a.foo() // 报错
```

## 继承

extends关键字可以创建一个继承了某个类的子类。
```js
class Animal {
  constructor() {
  }
  run() {
    console.log('run')
  }
  speak(){
    console.log('haha')
  }
}
class Duck extends Animal{
  speak() {
    console.log('gaga')
  }
}
let foo = new Duck()
foo.speak() // 'gaga'
foo.run() // 'run'
```

子类中，super关键字用来访问父类的构造函数或者方法。
```js
class Animal {
  constructor(name) {
    this.name = name
  }
  ha() {
    console.log('ha')
  }
  speak(){
    console.log('haha')
  }
}
class Duck extends Animal{
  constructor(name) {
    super(name) // 调用父类的构造函数
  }
  speak() {
    super.ha() //调用父类的方法，静态的也能用。
    console.log(this.name + ':gaga')
  }
}
let foo = new Duck('foo')
foo.speak() // 'ha' 'foo:gaga'
```

目前想到这么多，有需要再补充。

**参考**
MDN上的各种文档，阮一峰的ES6入门。
