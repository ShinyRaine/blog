---
title: 将文件转成URL的方式
date: 2018-01-15 16:20:58
tags: [javascript]
---

做文件预览的时候一般思路是拿到input中的文件对象，将它转成URL塞进对应的HTML标签中。
查阅资料发现这个需求有很多种实现方式。

<!--more-->
## URL.createObjectURL()
```js
var input = document.getElementsByTagName('input')[0];
var blobsrc = URL.createObjectURL(input.files[0]);
console.log(blobsrc); // blob:{location.href}/62f9ca74-aeba-4744-88f5-15d3807f71ad
```

该方法会创建一个新的URL对象，这个URL对象表示参数中的文件对象。这个URL的生命周期和创建它的窗口中的 document 绑定，当窗口关闭的时候URL对象会被释放，相应的url也就失效了。

在每次调用 createObjectURL() 方法时，都会创建一个新的 URL 对象。当不再需要这些 URL 对象时，每个对象必须通过调用 URL.revokeObjectURL() 方法来释放。

## FileReader API
```js
var input = document.getElementsByTagName('input')[0];
var reader = new FileReader();
reader.readAsDataURL(input.files[0]); 
img.src = src;
reader.onload = function(e){ 
    console.log(e.target.result); // data:image/png;base64,....
}
```

readAsDataURL方法会读取指定的 Blob 或 File 对象。读取操作完成的时候，readyState 会变成已完成（DONE），并触发 loadend 事件，同时 result 属性将包含一个data:URL格式的字符串（base64编码）以表示所读取文件的内容。


