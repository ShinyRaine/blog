---
title: react组件的生命周期
date: 2017-08-14 19:37:23
tags: [react,javascript]
---
# 生命周期
自定义组件(ReactCompositeComponent)的生命周期主要通过 3 个阶段进行管理—— MOUNTING、RECEIVE_PROPS 和 UNMOUNTING，它们负责通知组件当前所处的阶段，应该执行生命周期中的哪个步骤。这 3 个阶段对应 3 种方法，分别为: mountComponent、updateComponent 和 unmountComponent，每个方法都提供了几种处理方法，其中带 will 前缀的方法在进入状态之前调用，带 did 前缀的方法在进入状态之后调用。
## 挂载
getInitialState、componentWillMount、render 和 componentDidMount。

### getDefaultProps()
static defaultProps = {}。getDefaultProps 是通过构造函数进行管理，只执行一次。

### getInitialState()
this.state = {}
在组件挂载之前调用。在组件的生命周期中仅执行一次，用于设置组件的初始化 state 。返回值将会作为 this.state 的初始值。
在使用es6写法中不需要写这个函数。初始化state要在class的constructor里直接写入this.state = {}。

### componentWillMount()
componentWillMount() 服务器端和客户端都只调用一次，在初始化渲染执行之前立刻调用。

### componentDidMount()
componentDidMount() 在初始化渲染执行之后立刻调用一次，仅客户端有效

## 更新
componentWillReceiveProps、shouldComponentUpdate、componentWillUpdate、render 和 componentDidUpdate。

### componentWillReceiveProps(object nextProps)
在组件接收到新的 props 的时候调用。

### shouldComponentUpdate(object nextProps, object nextState)
在接收到新的 props 或者 state，将要渲染之前调用。一般返回ture。
如果性能是个瓶颈，尤其是有几十个甚至上百个组件的时候，使用 shouldComponentUpdate 可以提升应用的性能。
如果自行改写了这个函数，一定要有布尔类型的返回值。

### componentWillUpdate()
组件更新的render前调用。

### componentDidUpdate(object prevProps, object prevState)
在组件的更新已经同步到 DOM 中之后立刻被调用。该方法不会在初始化渲染的时候调用。

## 移除
### componentWillUnmount()
在组件从 DOM 中移除的时候立刻被调用。在该方法中执行任何必要的清理。
所有更新队列和更新状态都被重置为 null，并清除了公共类，完成组件卸载操作。

## setState
在 will 前缀的生命周期函数中调用setState是不会触发re render，而是会进行 state 合并。
在render前的生命周期函数中this.state不是最新的。
