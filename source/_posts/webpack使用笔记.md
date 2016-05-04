---
title: webpack使用笔记
date: 2016-04-19 17:05:37
tags: [webpack]
---
从各路博文/文档中整理而来。[官方文档](http://webpack.github.io/docs/)还是值得一看的。虽然我一直是中文党。

## 它的用途
简单来说webpack是一个把各路写法乱七八糟的JS，CSS通过不同的模块加载器打包成几个文件的工具。
看看从别处找来的特点总结

1. 对 CommonJS 、 AMD 、ES6的语法做了兼容
2. 对js、css、图片等资源文件都支持打包
3. 串联式模块加载器以及插件机制，让其具有更好的灵活性和扩展性，例如提供对CoffeeScript、ES6的支持
4. 有独立的配置文件webpack.config.js
5. 可以将代码切割成不同的chunk，实现按需加载，降低了初始化时间   //暂时没用过
6. 支持 SourceUrls 和 SourceMaps，易于调试    //暂时没用过
7. 具有强大的Plugin接口，大多是内部插件，使用起来比较灵活
8. webpack 使用异步 IO 并具有多级缓存。这使得 webpack 很快且在增量编译上更加快。

## 写法
简单地写一个webpack.config.js，描述都写注释里啦
<!--more-->
```
var webpack = require('webpack')
var ExtractTextPlugin = require('extract-text-webpack-plugin');    //一个CSS加载插件

module.exports = {
  entry: './index.js',            //入口文件
  output: {
    path: __dirname + 'assets',   //输出文件路径
    filename: 'bundle.js',        //输出文件名
    publicPath: '/assets/'        //访问输出文件的路径
  },
  plugins: [
    new ExtractTextPlugin('style.css', {
      disable: false,
      allChunks: true
    }),
  ],
  module: {
    loaders: [                     //模块加载器
      {
        test: /\.css$/,
        loader: ExtractTextPlugin.extract('style-loader', 'css-loader'),
        exclude: __dirname + 'components'
      },
      {
        test: /\.js$/,             //使用加载器加载的文件名
        loaders: [ 'babel' ],      //加载器
        exclude: /node_modules/,   //排除不处理的目录
        include: __dirname         //要处理的目录
      },
    ]
  }
}

```
