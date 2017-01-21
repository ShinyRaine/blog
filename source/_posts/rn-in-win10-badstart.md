---
title: react-native在win10上搭建安卓开发环境踩到的坑
date: 2017-01-21 19:37:23
tags: [react-native, javascript]
---
一直想尝试使用rn开发，然而太穷没有mac。看官方文档支持windows系统，虽然网上资料很少，想着尝试一下也未尝不可。
然而。。。
折腾了两天以后，第一次写出个hello world还开心的不行，甚至想发博文纪念。
好了，现在就把我踩的坑列出来吧。
## 参考资料
要在windows上搭建安卓开发环境参考首选[react native中文网-搭建开发环境](http://reactnative.cn/docs/0.40/getting-started.html#content)。
另外参考rn中文网的文字教程搭建helloworld[在Windows下搭建React Native Android开发环境](http://bbs.reactnative.cn/topic/10/)。
<!--more-->
我遇到的大部分问题在这两篇中得到了解决。
## 找个大磁盘装AndroidStudio和SDK
一开始图方便就默认设置一路next，结果装着装着硬盘不够了。
确实我C盘余量比较小，只有30多G，但那也大的太夸张了吧，顶个小型游戏了。
## 配置各种系统变量
如果你和我一样除了前端什么都没开发过，那你也需要添加下面这些系统变量。
```
JAVA_HOME: xxx\Java\jdk1.8.0_121
CLASSPATH: .;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar
android:  xxx\Android\sdk\platform-tools;xxx\Android\sdk\tools;
ANDROID_HOME: xxx\Android\sdk
PATH: xxx\Java\jdk1.8.0_121\bin;xxx\Java\jdk1.8.0_121\jre\bin;%android%
```
其中xxx代表安装路径；PATH里面这三个是需要添加到原来的变量里的。
## gradle下载出错
如果运行react-native run-android，在一堆.........后停止并报错了，那八成是gradle下载出错了。
我的解决方法是把gradle包下载下来。
在一堆......之前找到地址并用迅雷下载。将压缩包放在`C:\Users\用户名\.gradle\wrapper\dists\gradle-版本号-all\一个随机字符串`的路径下，删除之前自动下载的不全的文件。然后再次运行react-native run-android。
## MIUI的坑
MIUI安全限制可真多，使用自己的小米手机做测试各种报install的错误。
gradle的报错还是蛮良心的，错误提示中很醒目的
`* What went wrong`
如果这句下面的错误提示里有install关键字，那么是你的手机坑爹了。
保证配置了android环境变量，在命令行中可以运行adb后开始设置手机。
在 设置/关于手机 界面中点几下MIUI版本号那栏打开开发者模式。
在 设置/更多设置/开发者选项 里开启USB调试，允许USB安装，再拉到最后把MIUI优化关闭。
这样APP就可以装进来了。
