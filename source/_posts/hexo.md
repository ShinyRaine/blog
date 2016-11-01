---
title: hexo博客的搭建和操作
date: 2016-03-15 17:20:23
tags: [hexo,markdown]
---

我参考了[这个博客](http://zipperary.com/)中的hexo系列和[官方中文文档](https://hexo.io/zh-cn/docs/)。这里对此进行总结和补充，方便以后忘了某些命令的时候查看。

## 首先我写一些自己要记的命令
建文章
```
$ hexo new <title>

```
写完后
```
$ hexo generate
$ hexo deploy
```
等同于
```
$ hexo g
$ hexo d

$ hexo generate -d
$ hexo g -d   //这个最简单，用它了！
```
## hexo博客的搭建
<!--more-->
hexo 是一个基于 node.js 的博客框架，先来介绍一下搭建方法。
首先安装 [node.js](https://nodejs.org) ，官网下载安装包安装就可以。然后运行 npm install -g hexo-cli即可完成。
安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。
```
$ hexo init <folder>
$ cd <folder>
$ npm install
```
这样本地站就搭建完毕了，可以运行 hexo server 在本地查看站点。

按照[gitpages](https://pages.github.com/)的方法建立github站点，把博客放进去。主目录的_config.yml文件里是网站的配置，在这个文件中定义博客的标题/语言等信息，找到deploy，换成下面的写法。
```
deploy:
  type: git
  repository: git@github.com:RizzleCi/rizzleci.github.io
  branch: master
```
改动之后，运行
```
hexo generate
hexo deploy
```
这样rizzleci.github.io已经成了我们的博客了
网站主题对应的配置项是theme，我的这个主题是在自带主题上修改完成的。主题在themes文件夹中。官方也有不少主题可供选择。

## 写作与markdown语法

输入hexo new <title>后会生成post的头部。下面的tags写的时候要这样写
```markdown
---
title: hexo博客的搭建和操作
date: 2016-03-15 17:20:23
tags: [hexo,markdown]
---
```
[简书的markdown语法指南](http://www.jianshu.com/p/q81RER)不错，[这个文档](http://wowubuntu.com/markdown/)也比较容易看。我这里就记一下常用的，省得自己每次写都去查。
查看更多`<!--more-->`来分割
标题是不同个数的#
列表:` - (无序) 1. (有序) `
链接:` [我的博客](http://rizzleci.github.io/) `
代码:` ` `
引用:` < `
斜体:` * `
粗体:` ** `
表格：
```
| 头        | 头           | 头   |
| --------- |:------------:| ---:|
| 内容      | 内容          | 内容 |
```

分割：
```
* * *

***

*****

- - -

---------------------------------------
```
图片：`![Alt text](/path/to/img.jpg)`
