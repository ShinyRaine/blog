---
title: react组件的生命周期
date: 2016-03-14 19:37:23
tags: [react,javascript]
---
来自文章[组件的详细说明和生命周期（Component Specs and Lifecycle）](http://reactjs.cn/react/docs/component-specs.html)的整理。便于自己理解。

## 初始化
### getInitialState()
在组件挂载之前调用。在组件的生命周期中仅执行一次，用于设置组件的初始化 state 。返回值将会作为 this.state 的初始值。

## 挂载
### componentWillMount()
componentWillMount() 服务器端和客户端都只调用一次，在初始化渲染执行之前立刻调用。

### componentDidMount()
componentDidMount() 在初始化渲染执行之后立刻调用一次，仅客户端有效
<!--more-->
## 更新
### componentWillReceiveProps(object nextProps)
在组件接收到新的 props 的时候调用。

### shouldComponentUpdate(object nextProps, object nextState)
在接收到新的 props 或者 state，将要渲染之前调用。一般返回ture。如果性能是个瓶颈，尤其是有几十个甚至上百个组件的时候，使用 shouldComponentUpdate 可以提升应用的性能。

### componentDidUpdate(object prevProps, object prevState)
在组件的更新已经同步到 DOM 中之后立刻被调用。该方法不会在初始化渲染的时候调用。

## 移除
### componentWillUnmount()
在组件从 DOM 中移除的时候立刻被调用。在该方法中执行任何必要的清理。
动态更新界面的关键点就是调用 this.setState() 。
