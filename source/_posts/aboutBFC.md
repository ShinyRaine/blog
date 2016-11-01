---
title: 关于BFC
date: 2016-09-22 19:46:46
tags: [css]
---
看了那么多博文，始终对这个概念有些迷惑，那么就来好好研究一下吧。

首先说一下FC，Formatting Context，即格式化上下文。这种规范定义了页面上的渲染区域，盒子都会被包含于一个格式化上下文。在CSS2规范里，一个盒不是属于块级上下文（BFC），就是属于内联上下文（IFC）。在后续新增规范（通常称为CSS3）中，又新增加了网格布局格式化上下文（GFC）和自适应格式化上下文（FFC）。这个文章里只说BFC，其他的后续研究。
<!--more-->
### BFC
BFC叫做Block Formatting Context，翻译为块级格式化上下文。
在文档中对它的定义非常简单，即<浮动，绝对定位元素，非块盒的块容器（例如，inline-blocks，table-cells和table-captions）和'overflow'不为'visible'的块盒（当该值已被传播到视口时除外）会为它们的内容建立一个新的块格式化上下文。>可以总结出下面几种触发BFC的方式：
- float: left/right
- position: absolute
- display: inline-block/table-cell/table-caption
- overflow: hidden/auto/scroll

在一个BFC中，块元素表现形式和它们平时在body中的表现形式一样，也可以说根元素就是一个BFC。这很普通。
然而BFC最大的特点在于与外部元素的关系：没有关系。
没错，作为一个BFC，它要尽力不影响外部元素，也不被外部元素所影响。正式因为这个，才有了一系列的神奇应用。

- 一个利用BFC的两栏布局:
``` html
<div class="div1"></div>
<div class="div2">
	一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字一些文字
</div>
```
```css
.div1{
	float: left;
	width: 100px;
	height: 200px;
	background: #c0e;
	margin-right: 10px;
}
.div2{
	overflow: hidden;
}
```
右边一栏变成BFC了就不会和左边的BFC重叠了。因为BFC不能被外部元素影响，如果在那个"一些文字"的div中添加一个clear:both的元素，它就会被浮动元素所影响。
- 利用BFC的清除浮动
```css
clearfix { overflow: hidden}
```
这样父元素变成BFC了就不能让它里面的元素溢出影响外面的东西了。

另外看到一个取消margin合并的应用。没有margin的合并不是很正常的事情么= =，我想不到这个的实际应用，这里不写了。