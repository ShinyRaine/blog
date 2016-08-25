---
title: css animation
date: 2016-08-18 11:09:19
tags: [css]
---
这篇是CSS动画的笔记。我发现我总是记不住东西，即使用过了一次，记录在这里省的每次都去mdn搜。

## animation
### animation-delay
延时，即从元素加载完成之后到动画序列开始执行的这段时间。
### animation-direction
动画重复形式。
- normal 默认
	每个动画循环结束，动画重置到起点重新开始。
<!--more-->
- alternate
	动画交替反向运行，反向运行时，动画按步后退，同时，带时间功能的函数也反向，比如，ease-in 在反向时成为ease-out。
- reverse
	反向运行动画，每周期结束动画由尾到头运行。
- alternate-reverse
	反向交替， 反向开始交替

### animation-duration
动画一个周期的时长。

### animation-iteration-count
动画重复次数，默认值为1.
- infinite 
	无限循环播放动画.
- <number>
	动画播放的次数 不可为负值. 可以用小数定义循环(0.5 将播放动画到关键帧的一半（from 0 ~ 50%).

### animation-name
指定由@keyframes描述的关键帧名称。

### animation-play-state
一个动画是否运行或者暂停。可以通过查询它来确定动画运行状态。另外，它的值可以被设置为暂停和恢复的动画的重放。
- running
	当前动画正在运行。
- paused
	当前动画以被停止。

### animation-timing-function
设置动画速度，即通过建立加速度曲线，设置动画在关键帧之间是如何变化。
可能值为一或多个 [<timing-function>](https://developer.mozilla.org/en-US/docs/Web/CSS/timing-function)。

### animation-fill-mode
指定动画执行前后目标元素应用样式。
- none
	动画执行前后不改变任何样式
- forwards
	动画最后一帧的样式
- backwards
	动画第一帧的样式

## 关键帧
@keyframes指定动画中特定时间点必须展现的关键帧样式
语法
```css
@keyframes name {
	from {
		transform: rotate(0);
	} 
	to{
		transform: rotate(180deg);
	}
}
```
```css
@keyframes name {
	0% {
		transform: rotate(0);
	} 
	50%{
		transform: rotate(180deg);
	}
	100%{
		transform: rotate(0);
	}
}
```
