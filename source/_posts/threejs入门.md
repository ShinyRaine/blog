---
title: threejs入门
date: 2016-03-03 19:39:36
tags: [threejs, javascript]
---
本文是一篇简单的webGL+threejs构建web三维视图的入门教程，你可以了解到利用threejs创建简单的三维图形，并且控制图形运动。若有不足，欢迎指出。
本文使用的框架是three.js
github地址：[https://github.com/mrdoob/three.js][1]，
官网：[http://threejs.org/][2]，
文档：[http://threejs.org/docs/][3]，

本文中的示例已上传github，地址：[https://github.com/RizzleCi/three.js-demo][4]

## 一、创建场景 ##
我们所见的视图由两个部分共同创建，scene和camera。
首先定义一个场景：

```javascript
var scene = new THREE.Scene();
```
<!--more-->
然后定义一个相机：

```javascript
var camera = new THREE.PerspectiveCamera( 90, width/height, 0.1, 1000 );
```

等等，定义相机需要视窗的长宽。现在我要让我的绘图显示在页面的一个区域（<div>)标签中。我们来选中这个元素，获取它的长宽。

```javascript
var container = document.getElementById('canvasdiv');
var width = canvasdiv.clientWidth;
var height = canvasdiv.clientHeight;
```

这样再加上前面的一行代码，我们就完成了对相机的定义。然后把相机位置设定在z轴上方便观察。
camera.position.set(0,0,10)

现在我们需要一个渲染器把定义好的场景渲染出来。

```javascript
var renderer = new THREE.WebGLRenderer();
```

给这个渲染器合适的大小。

```javascript
renderer.setSize( width, height );
```

然后将其加入到dom中。

```javascript
canvasdiv.appendChild( renderer.domElement );
```

（运行以后发现这其实就是一个canvas元素。其实我们也可以在html中创建canvas元素再将renderer绑定到它上面。`var renderer = new THREE.WebGLRenderer({ canvas: document.getElementById('mainCanvas') });`）
最后进行渲染。

```javascript
renderer.render(scene,camera);
```

这样，就建立了一个简单的3d场景。
## 二、绘制图形 ##
我将threejs中的物体理解为模型+材料。以一个长方体为例。
创建模型：

```javascript
var geometry = new THREE.BoxGeometry( 1,2,1 );
```

定义材料：

```javascript
var material = new THREE.MeshBasicMaterial( { color: 0x645d50 } );
```

有了这两者，我们就可以构建一个长方体方块了。

```javascript
var cube = new THREE.Mesh( geometry, material );
```

我们将其添加到场景中显示。

```javascript
scene.add( cube );
```

这样，一个三维的长方体就绘制完成了
关于其他形状的绘制，张雯莉的threejs入门指南中介绍的很详细，在此不多赘述。
这部分的完整代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style type="text/css">
#canvasdiv{
	width: 600px;
	height: 600px;
}

	</style>
    <script src="js/three.min.js"></script>
</head>
<body>


	<div class="main-content" id="canvasdiv">

	</div>


<script type="text/javascript" >
var container = document.getElementById('canvasdiv')
var scene = new THREE.Scene();
var width = canvasdiv.clientWidth;
var height = canvasdiv.clientHeight;
var camera = new THREE.PerspectiveCamera( 90, width/height, 0.1, 1000 );
camera.position.set(0,0,10);
var renderer = new THREE.WebGLRenderer();
renderer.setSize( width, height );
canvasdiv.appendChild( renderer.domElement );
var geometry = new THREE.BoxGeometry( 2,1,1 );
var material = new THREE.MeshBasicMaterial( { color: 0x645d50 } );
var cube = new THREE.Mesh( geometry, material );
scene.add( cube );
renderer.render(scene,camera);
</script>
</body>
</html>
```
看上去它只是一个长方形而已，但是它确实是一个立体图形，你可以改变一下camera的位置来观察一下。
```
camera.position.set( 5,3,10 );
```
好了，这样看起来是一个立体的长方体了吧。
## 三、创建3d对象 ##
大多数时候，我们需要讲绘制的图形整合到一起进行控制。此时，我们便需要一个3d对象。
### 创建一个自己的对象 ###
在上面绘制的那个长方体上面再放一个球。
```
var geometry = new THREE.SphereGeometry( 0.5,100,100 );
var material = new THREE.MeshBasicMaterial( { color: 0xb9c16c } );
var ball = new THREE.Mesh( geometry,material );
ball.position.set( 0,0,1 );
scene.add(ball);
```
另说一句，默认放置mesh的位置是( 0,0,0 )，和改变相机位置一样，我们可以用ball.position.set方法来改变图形或对象的位置。因此动画也利用这个方法来实现。
然后要把它们整合成一个对象。
首先我们创建一个对象。

```javascript
var myobj = new THREE.Object3D();
```
然后把我们画的图形添加到对象里就ok啦。

```javascript
myobj.add( cube );
myobj.add( ball );
```
这时候我们已经有了一个3d对象，它包含我们刚刚绘制的长方形和球。于是就没有必要像原来那样把图形一个一个地放置到场景里，只需要把刚刚创建的对象放置到场景里。

```javascript
scene.add( myobj );
```

完整代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style type="text/css">
#canvasdiv{
	width: 600px;
	height: 600px;
}

	</style>
    <script src="js/three.min.js"></script>

</head>
<body>


	<div class="main-content" id="canvasdiv">

	</div>


<script type="text/javascript" >
var container = document.getElementById('canvasdiv')
var scene = new THREE.Scene();
var width = canvasdiv.clientWidth;
var height = canvasdiv.clientHeight;
var camera = new THREE.PerspectiveCamera( 90, width/height, 0.1, 1000 );
camera.position.set( 0,0,10 )
var renderer = new THREE.WebGLRenderer();
renderer.setSize( width, height );
canvasdiv.appendChild( renderer.domElement );

var geometry = new THREE.BoxGeometry( 2,1,1 );
var material = new THREE.MeshBasicMaterial( { color: 0x645d50 } );
var cube = new THREE.Mesh( geometry, material );
//scene.add( cube );

var geometry = new THREE.SphereGeometry( 0.5,100,100 );
var material = new THREE.MeshBasicMaterial( { color: 0xb9c16c } );
var ball = new THREE.Mesh( geometry,material );
ball.position.set( 0,0,1 );
//scene.add(ball);

var myobj = new THREE.Object3D();
myobj.add( cube );
myobj.add( ball );
scene.add( myobj );

renderer.render(scene,camera);
</script>
</body>
</html>
```
### 外部导入.obj文件 ###
threejs支持从外部导入.obj文件，听说这种文件是用3DsMax绘制的，用PS也可以编辑。我们需要引入OBJMTLLoader.js，MTLLoader.js文件；也有一个OBJLoader.js，但利用这个库只能导入模型而不能导入绘制obj时添加的材质，个人感觉不是非常实用，就不做介绍了。这时候，我们需要把文件们放到一个服务器上，否则会出现跨域问题。
为了让图像更明显，我们添加一些光线。

```javascript
scene.add( new THREE.AmbientLight( 0xffffff ) );
```
这里，我们通过导入图片来设置这个对象的纹理。

```javascript
var texture = new THREE.Texture();
var loader = new THREE.ImageLoader( );
loader.load( 'tank.jpg', function ( image ) {
	texture.image = image;
	texture.needsUpdate = true;
} );
```
开始导入我们的3D对象！

```javascript
var loader = new THREE.OBJMTLLoader();  
loader.load('tank.obj','tank.mtl',function(object){
	tank = object;

	object.traverse(function(child){
        if (child instanceof THREE.Mesh){
        //将贴图赋于材质
            child.material.map = texture;
            child.material.transparent = true;
        }
    });
    object.position.set(0,0,0);
    scene.add( object );  
    camera.lookAt( object.position );
	renderer.render( scene,camera );  
});  
```
模型导入进去了，但是看起来还是很奇怪，这就要我们加一些其他光线渲染一下。在这里我们添加平行光线。

```javascript
var directionalLight = new THREE.DirectionalLight( 0xffffff, 1.5 );
directionalLight.position.set( 1, 1, 1 )
scene.add( directionalLight );
```
变得光泽多了。
从外部导入obj的完整代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style type="text/css">
#canvasdiv{
	width: 1200px;
	height: 800px;
}

	</style>
    <script src="js/three.min.js"></script>
	<script src="js/MTLLoader.js"></script>  
    <script src="js/OBJMTLLoader.js"></script>
</head>
<body>


	<div class="main-content" id="canvasdiv">

	</div>


<script type="text/javascript" >
var container = document.getElementById('canvasdiv')
var scene = new THREE.Scene();
var width = canvasdiv.clientWidth;
var height = canvasdiv.clientHeight;
var camera = new THREE.PerspectiveCamera( 90, width/height, 0.1, 1000 );
camera.position.set( -10,10,10);
var renderer = new THREE.WebGLRenderer();
renderer.setSize( width, height );
canvasdiv.appendChild( renderer.domElement );


var texture = new THREE.Texture();
var loader = new THREE.ImageLoader(  );
loader.load( 'tank.jpg', function ( image ) {

	texture.image = image;
	texture.needsUpdate = true;

} );
var loader = new THREE.OBJMTLLoader();  
loader.load('tank.obj','tank.mtl',function(object){
	tank = object;
	object.traverse(function(child){
        if (child instanceof THREE.Mesh){
            //将贴图赋于材质
            child.material.map = texture;
            //重点，没有该句会导致PNG无法正确显示透明效果
            child.material.transparent = true;
        }
    });
    object.position.set(0,0,0);
    scene.add( object );  
    camera.lookAt( object.position );
	renderer.render( scene,camera );
});  
scene.add( new THREE.AmbientLight( 0xffffff ) );
var directionalLight = new THREE.DirectionalLight( 0xffffff, 1.5 );
directionalLight.position.set( 1, 1, 1 )
scene.add( directionalLight );

renderer.render(scene,camera);
</script>
</body>
</html>
```
## 四、动画 ##
现在我们想办法让这些图形动起来。
在threejs中运用最多的动画是用requestAnimationFrame()方法。也可以利用传统的setInterval()做，但用这个会掉帧。
这里我们做一个render函数，来进行渲染和动画调用。这里以前面添加了myobj对象的代码为基础。
### 基本的动画 ###
现在来不断地改变对象的角度方便对其进行观察。

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style type="text/css">
#canvasdiv{
	width: 600px;
	height: 600px;
}

	</style>
    <script src="js/three.min.js"></script>

</head>
<body>


	<div class="main-content" id="canvasdiv">

	</div>


<script type="text/javascript" >
var container,camera,scene,renderer,myobj;

var init = function () {
	container = document.getElementById('canvasdiv')
	scene = new THREE.Scene();
	var width = canvasdiv.clientWidth;
	var height = canvasdiv.clientHeight;
	camera = new THREE.PerspectiveCamera( 90, width/height, 0.1, 1000 );
	camera.position.set( 0,0,10 )
	renderer = new THREE.WebGLRenderer();
	renderer.setSize( width, height );
	canvasdiv.appendChild( renderer.domElement );

	var geometry = new THREE.BoxGeometry( 2,1,1 );
	var material = new THREE.MeshBasicMaterial( { color: 0x645d50 } );
	var cube = new THREE.Mesh( geometry, material );
	//scene.add( cube );

	var geometry = new THREE.SphereGeometry( 0.5,100,100 );
	var material = new THREE.MeshBasicMaterial( { color: 0xb9c16c } );
	var ball = new THREE.Mesh( geometry,material );
	ball.position.set( 0,0,1 );
	//scene.add(ball);

	myobj = new THREE.Object3D();
	myobj.add( cube );
	myobj.add( ball );
	scene.add( myobj );
}


var render = function () {
	requestAnimationFrame( render );
	myobj.rotation.x+=0.01;
	myobj.rotation.y+=0.01;
	renderer.render(scene,camera);
}
init()
render()
</script>
</body>
</html>
```
### 对动画进行控制 ###
接着让我们对动画进行控制。
在我实现的项目中，是通过websocket连接后台传入参数来控制对象运动的，这里就介绍一下使用参数控制吧。
这里有一个问题，就是requestAnimationFrame回调的函数不能带有参数，否则会出现奇怪的bug。所以我选择用一个全局对象来进行控制。

```javascript
var control={
	s:0,
	p:0,
	q:0,
	j:0,
}
```
这里s是运动的速度，p,q,j分别是myobj将要运动到的位置的x,y,z坐标。我们先写一个控制它在x轴上运动的函数：

```javascript
var run = function () {
	if ( myobj.position.x<control.p ) {
		myobj.position.x += control.s;
		requestAnimationFrame( run );
		renderer.render( scene,camera )
	};
	if ( myobj.position.x>control.p ) {
		myobj.position.x -= control.s;
		requestAnimationFrame( run );
		renderer.render( scene,camera )
	};
}
```
再在render函数中添加对run的调用requestAnimationFrame( run )。这样就可以在命令行中改变对象control的值实现控制myobj的运动。但是在运动停止后我的浏览器为什么会变得很卡，而且运动速度回有变化。我还不知道原因。不知道有没有朋友和我有同样的问题。于是我把函数拆成了两个，这样浏览器性能好些。

```javascript
var run = function () {

	if ( myobj.position.x<control.p ) {
		myobj.position.x += control.s;			
		requestAnimationFrame( run );
	};
		renderer.render( scene,camera )
}
var runx = function () {

	if ( myobj.position.x>control.p ) {
	myobj.position.x -= control.s;			
		requestAnimationFrame( runx );
	};
		renderer.render( scene,camera )
}
```
同样的，也可以写出在y,z轴上运动的函数。
在x轴上运动的完整代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style type="text/css">
#canvasdiv{
	width: 600px;
	height: 600px;
}

	</style>
    <script src="js/three.min.js"></script>

</head>
<body>


	<div class="main-content" id="canvasdiv">

	</div>


<script type="text/javascript" >
var container,camera,scene,renderer,myobj;

var init = function () {
	container = document.getElementById('canvasdiv')
	scene = new THREE.Scene();
	var width = canvasdiv.clientWidth;
	var height = canvasdiv.clientHeight;
	camera = new THREE.PerspectiveCamera( 90, width/height, 0.1, 1000 );
	camera.position.set( 0,0,10 )
	renderer = new THREE.WebGLRenderer();
	renderer.setSize( width, height );
	canvasdiv.appendChild( renderer.domElement );

	var geometry = new THREE.BoxGeometry( 2,1,1 );
	var material = new THREE.MeshBasicMaterial( { color: 0x645d50 } );
	var cube = new THREE.Mesh( geometry, material );
	//scene.add( cube );

	var geometry = new THREE.SphereGeometry( 0.5,100,100 );
	var material = new THREE.MeshBasicMaterial( { color: 0xb9c16c } );
	var ball = new THREE.Mesh( geometry,material );
	ball.position.set( 0,0,1 );
	//scene.add(ball);

	myobj = new THREE.Object3D();
	myobj.add( cube );
	myobj.add( ball );
	scene.add( myobj );
}

var control={
	s:0,
	p:0,
	q:0,
	j:0,
}

var run = function () {

	if ( myobj.position.x<control.p ) {
		myobj.position.x += control.s;			
		requestAnimationFrame( run );
	};
		renderer.render( scene,camera )
}
var runx = function () {

	if ( myobj.position.x>control.p ) {
	myobj.position.x -= control.s;			
		requestAnimationFrame( runx );
	};
		renderer.render( scene,camera )
}

var render = function () {
	requestAnimationFrame( run );
	requestAnimationFrame( runx );
	renderer.render(scene,camera);
}


init()
render()
</script>
</body>
</html>
```
这个入门教程就到这里了，感谢阅读。


  [1]: https://github.com/mrdoob/three.js
  [2]: http://threejs.org/
  [3]: http://threejs.org/docs/
  [4]: https://github.com/RizzleCi/three.js-demo
