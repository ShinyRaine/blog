---
title: FormData的传输
date: 2018-02-24 19:46:46
tags: [javascript, 网络]
---

我们都知道请求头中的Content-Type用于指示资源的MIME类型。multipart/form-data类型的Content-Type中必须包含一个boundary字段。


<!--more-->

```
Content-Type: multipart/form-data; boundary=something
```

*media-type*
资源或数据的 MIME type 。
*charset*
字符编码标准。
*boundary*
对于多部分实体，boundary 是必需的，其包括来自一组字符的1到70个字符。

## boundary
在请求body中，boundary起到分割参数的作用。

```html
<form action="/update" method="post" enctype="multipart/form-data">  
  <input type="text" name="a" />
  <input type="text" name="b" />
  <button type="submit">submit</button>
</form>
```
请求头中Content-Type如下
```
Content-Type:multipart/form-data; boundary=----WebKitFormBoundaryn7ulZmAtRx5qtH8y
```

请求body如下：
```
------WebKitFormBoundaryn7ulZmAtRx5qtH8y
Content-Disposition: form-data; name="a"

a
------WebKitFormBoundaryn7ulZmAtRx5qtH8y
Content-Disposition: form-data; name="b"

b
------WebKitFormBoundaryn7ulZmAtRx5qtH8y--
```
## 规范

FormData的规范在rfc1867中。

关于boundary，规范中的描述是选择一个不在数据中出现的boundary。一般来说使用一段从概率上说“几乎不可能”的数据即可。

表单中的每个字段按其原来的顺序以流的形式发送。规范规定了媒体内容遵循RFC 1521中的数据流规则。每一个字段都有一个唯一的name。form-data的body中有多个部分，每个部分都有 Content-Disposition作为开头，值为form-data和表单中的name。name中有非ASCII码字符的话，还应该按照RFC 1522里面所规定的方法进行编码。文件input会识别出原文件名，原文件名会被添加到Content-Disposition的filename参数中。

## 后端处理方法
express文档中提到了用multer来处理form-data。它创建出一个中间件，其中使用busboy解析出文件对象并进行存储。

busboy解析header中的content-type，将boundary分离出来，并使用它和其他一系列参数创建Dicer对象。
根据content-disposition解析出文件名、原文件名、mimetype等信息。

Dicer分离出数据流中的header部分和文件/数据内容部分，触发header事件和part事件。

StreamSearch使用Boyer-Moore-Horspool算法，用于在流数据中检索字符串，并根据此分割数据。

相关代码如下：

*busboy*
```js
// 各种类型数据的解析器，form-data所使用的是multipart
var TYPES = [
  require('./types/multipart'),
  require('./types/urlencoded'),
];
// 提取出content-type的内容
var parsed = parseParams(headers['content-type']),  // 如["multipart/form-data", ["boundary", "----WebKitFormBoundary6KeKkA89uHEIkD6z"]]
    matched, type;
for (var i = 0; i < TYPES.length; ++i) {
  type = TYPES[i];
  if (typeof type.detect === 'function')
    matched = type.detect(parsed);
  else
    matched = type.detect.test(parsed[0]);
  if (matched)
    break;
}

if (matched) {
  var cfg = { parsedConType: parsed, .... };
  // 生成解析器
  this._parser = type(this, cfg);
  return;
}

```
parseParams将字符串转换为数组，它会将如"multipart/form-data; boundary=----WebKitFormBoundarypkgBw5NX2PSvvBA0"的content-type值转换为`["multipart/form-data", ["boundary", "----WebKitFormBoundary6KeKkA89uHEIkD6z"]]`

在'./types/multipart'中，可以看到如果是form-data的话detect为一个正则表达式，匹配Content-Type的一部分。
```js
Multipart.detect = /^multipart\/form-data/i;
```
当 matched 成功的时候，定义_parser为此时的type。

使用Dicer库生成parser，刚刚拿到的boundary被存在parserCfg对象中。
Multipart触发自身的file或field事件，将文件、解析出的字符串传入回调函数。
```js
this.parser = new Dicer(parserCfg);
this.parser.on('part', function onPart(part) {

  part.on('header', function(header) {
    // 解析文件名、类型等数据
    //
    if (contype === 'application/octet-stream' || filename !== undefined) {
      // 文件
      // file是一个stream.Readable对象
      boy.emit('file', fieldname, file, filename, encoding, contype);
      onData = function(data){...} // 将数据push到file
      onEnd = function(){...} // 清理
    } else {
      // 普通参数
      boy.emit('field', fieldname, buffer, false, truncated, encoding, contype);
      onData = function(data){...} // 将二进制Buffer数据转为字符串
      onEnd = function(){...} // 清理
    }
  })
  part.on('data', onData);
  part.on('end', onEnd);
})

```

*Dicer*
Dicer是一个流式数据的分解器。
Dircer中利用boundary生成一个StreamSearch对象，并监听它的info事件。
```js
this._bparser = new StreamSearch('\r\n--' + boundary);
this._bparser.on('info', function(isMatch, data, start, end) {
  self._oninfo(isMatch, data, start, end);
});
Dicer.prototype._oninfo = function(isMatch, data, start, end){
  ...
  if (!this._part) {
    ...
    this._part = new PartStream(this._partOpts); // 可读流
    this._part._read = function(n) {
      self._unpause();
    };
    ev = this._isPreamble ? 'preamble' : 'part';
    if (this._events[ev])
      this.emit(ev, this._part); // 触发part事件
    else
      this._ignore();
    if (!this._isPreamble)
      this._inHeader = true;
  }
  if (data && start < end && !this._ignoreData) {
    // push data
  }
  ...
    ...// 清理
  }
}


this._hparser = new HeaderParser(cfg);
this._hparser.on('header', function(header) {
  self._inHeader = false;
  self._part.emit('header', header);
});
// HeaderParser 文件
this.ss = new StreamSearch(B_DCRLF); //B_DCRLF = new Buffer('\r\n\r\n')
this.ss.on('info', function(isMatch, data, start, end) {
  if (data && !self.maxed) {
    ...
    self.buffer += data.toString('binary', start, end);
  }
  if (isMatch)
    self._finish();
});
HeaderParser.prototype._finish = function() {
  ...// 清理工作
  var header = this.header;
  this.emit('header', header);
};
```
*StreamSearch*
StreamSearch是二进制流的一个查询器，使用push方法输入需要查询的数据，并监听它的info事件，它会将匹配结果传入info事件的回调函数中。
