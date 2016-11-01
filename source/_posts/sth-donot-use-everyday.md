---
title: 不常用的知识整理
date: 2016-07-21 08:47:05
tags: [css,javascript,html]
---
很多整理自以前的博客，算是记录一下自己记不住的东西了~
嗯，这篇文章应该会持续更新
## user-modify
这个东西要加浏览器前缀才能用：-webkit-user-modify -moz-user-modify
值有  read-only（只读） read-write（可读写） read-write-plaintext-only（可读写文本）。据说老版本的火狐还支持write-only，既然现在都不支持这个了还是忽略它吧。
张鑫旭的[这一篇文章](http://www.zhangxinxu.com/wordpress/2016/01/contenteditable-plaintext-only/)详细介绍了该属性和另一个可以让html可编辑的属性：contenteditable。
<!--more-->
## Sublime Text 3包管理
Ctrl + ~打开命令行 输入
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
重启
shift + ctrl + p 输入 install 可找到安装package，输入remove可以找到卸载package的

### 一些包
ConvertToUTF8 GBK转UTF8编码
babel Snippets react语法高亮

## ubuntu升级内核后VB虚拟机不能启动 
运行sudo /etc/init.d/vboxdrv setup 重新安装

## 兼容hack


| hack      |	写法	  | IE6(S) |IE6(Q) |IE7(S) |IE7(Q)| IE8(S) |IE8(Q) |IE9(S) |IE9(Q) |IE10(S)|IE10(Q)|
| ----------|:-----------:|:------:|:-----:|:-----:|:----:|:------:|:-----:|:-----:|:-----:|:-----:| -----:|
| *         |	*color	  |   Y    |   Y   |   Y   |  Y   |   N    |   Y   |   N   |   Y   |   N   |   Y   |
| +         |	+color	  |   Y    |   Y   |   Y   |  Y   |   N    |   Y   |   N   |   Y   |   N   |   Y   |
| -         |	-color	  |   Y    |   Y   |   N   |  N   |   N    |   N   |   N   |   N   |   N   |   N   |
| _         |	_color	  |   Y    |   Y   |   N   |  Y   |   N    |   Y   |   N   |   Y   |   N   |   N   |
| #         |	#color	  |   Y    |   Y   |   Y   |  Y   |   N    |   Y   |   N   |   Y   |   N   |   Y   |
|\0	        |color:red\0  |	  N    |   N   |   N   |  N   |   Y    |   N   |   Y   |   N   |   Y   |   N   |
|\9\0       |color:red\9\0|	  N    |   N   |   N   |  N   |   N    |   N   |   Y   |   N   |   Y   |   N   |
*html *前缀只对IE6生效
*+html *+前缀只对IE7生效
@media screen\9{...}只对IE6/7生效
@media \0screen {body { background: red; }}只对IE8有效
@media \0screen\,screen\9{body { background: blue; }}只对IE6/7/8有效
@media screen\0 {body { background: green; }} 只对IE8/9/10有效
@media screen and (min-width:0\0) {body { background: gray; }} 只对IE9/10有效
@media screen and (-ms-high-contrast: active), (-ms-high-contrast: none) {body { background: orange; }} 只对IE10有效

## Polyfill?

polyfill 是“在旧版浏览器上复制标准 API 的 JavaScript 补充”,可以动态地加载 JavaScript 代码或库，在不支持这些标准 API 的浏览器中模拟它们。
例如，geolocation（地理位置）polyfill 可以在 navigator 对象上添加全局的 geolocation 对象，还能添加 getCurrentPosition 函数以及“坐标”回调对象，
所有这些都是 W3C 地理位置 API 定义的对象和函数。因为 polyfill 模拟标准 API，所以能够以一种面向所有浏览器未来的方式针对这些 API 进行开发，
一旦对这些 API 的支持变成绝对大多数，则可以方便地去掉 polyfill，无需做任何额外工作。

## 列举IE与其他浏览器不一样的特性？
1、事件不同之处：

触发事件的元素被认为是目标（target）。而在 IE 中，目标包含在 event 对象的 srcElement 属性；

获取字符代码、如果按键代表一个字符（shift、ctrl、alt除外），IE 的 keyCode 会返回字符代码（Unicode），DOM 中按键的代码和字符是分离的，要获取字符代码，需要使用 charCode 属性；

阻止某个事件的默认行为，IE 中阻止某个事件的默认行为，必须将 returnValue 属性设置为 false，Mozilla 中，需要调用 preventDefault() 方法；

停止事件冒泡，IE 中阻止事件进一步冒泡，需要设置 cancelBubble 为 true，Mozzilla 中，需要调用 stopPropagation()；

## 一个页面从输入 URL 到页面加载显示完成，这个过程中都发生了什么？
  详细版：
    1、浏览器会开启一个线程来处理这个请求，对 URL 分析判断如果是 http 协议就按照 Web 方式来处理;
    2、调用浏览器内核中的对应方法，比如 WebView 中的 loadUrl 方法;
    3、通过DNS解析获取网址的IP地址，设置 UA 等信息发出第二个GET请求;
    4、进行HTTP协议会话，客户端发送报头(请求报头);
    5、进入到web服务器上的 Web Server，如 Apache、Tomcat、Node.JS 等服务器;
    6、进入部署好的后端应用，如 PHP、Java、JavaScript、Python 等，找到对应的请求处理;
    7、处理结束回馈报头，此处如果浏览器访问过，缓存上有对应资源，会与服务器最后修改时间对比，一致则返回304;
    8、浏览器开始下载html文档(响应报头，状态码200)，同时使用缓存;
    9、文档树建立，根据标记请求所需指定MIME类型的文件（比如css、js）,同时设置了cookie;
    10、页面开始渲染DOM，JS根据DOM API操作DOM,执行事件绑定等，页面显示完成。

  简洁版：
    浏览器根据请求的URL交给DNS域名解析，找到真实IP，向服务器发起请求；
    服务器交给后台处理完成后返回数据，浏览器接收文件（HTML、JS、CSS、图象等）；
    浏览器对加载到的资源（HTML、JS、CSS等）进行语法解析，建立相应的内部数据结构（如HTML的DOM）；
    载入解析到的资源文件，渲染页面，完成。
## 移动端最小触控区域
44*44pt