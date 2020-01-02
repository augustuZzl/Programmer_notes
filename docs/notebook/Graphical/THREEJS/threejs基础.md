### 四大组件

#### 一：场景 Scene

场景就是舞台，你可以把任何东西放到场景的任何位置。用来容纳所有的 3D 对象。

`Three.Scene()`

#### 二：相机 Camera

浏览器中所显示的就是相机所拍摄的场景

1. 透视相机：类似于人眼，观察的事物是远大近小的： 

   `THREE.PersrectiveCamera(fov, aspect, near, far)`

   - fov：可以理解为视角，显示的场景范围，当为 0 时，可以理解为闭眼；一般人的视角是 45 **度**左右
   - aspect：几乎总是宽度比高度。就比如显示器总是宽的，手机看视频也要横过来一样
   - near、far 近远平面：意味着比这个 near 更近或者比 far 更远的将不会被渲染

   例：`var camers = new THREE.PersrectiveCamera(40, width/heigth, 1, 100);`

   表示视角为 40 度，充满整个窗口，最近看的距离为 1，最远能看到 100

2. 正投影相机：远近一样大： 

   ` THREE.OrthographicCamera( left, right, top, bottom, near, far ) `

   这六个参数形成一个长方体，很好理解了

#### 三：渲染器 Renderer

如何将场景渲染到屏幕上。计算的过程就叫做渲染。

`THREE.WebGLRenderer()`

#### 四：几何体 Object

即场景中的对象模型

### Hello World

#### 设置页面

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>My first Demo</title>
		<style>
			body { margin: 0; }
			canvas { width: 100%; height: 100% }
		</style>
	</head>
	<body>
		<script src="three.js"></script>
		<script>
			// Our Javascript will go here.
		</script>
	</body>
</html>
```

- three.js 程序渲染的场景实际上是通过 Canvas 画在浏览器上的

#### 创建场景

现在需要这三个组件 scene、camera、renderer：render the scene width camera

```javascript
// 场景
var scene = new THREE.Scene();
// 相机
var camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
// 渲染器
var renderer = new THREE.WebGLRenderer();
// 设置渲染的范围
renderer.setSize(window.innerWidth, window.innerHeight);
// 将渲染的场景放到 html 页面中
document.body.appendChild(renderer.domElement);
```

- setSize(width / 2, height / 2)：只渲染整个 Canvas 容器左上角 1/4 部分
- setSize(width / 2, height / 2, false)：也会渲染正给我 Canvas 容器，只不过这就像是拉伸充满的，分辨率( resolution )较低（即显得模糊）。这在考虑性能的应用中起一些作用
- renderer.domElement：即 canvas 标签

#### 添加几何体

THREE.Geometry() 几何体是一个包含三维数据的数据结构：

- 点：this.vertices = []
- 颜色：this.colors = []
- 面：this.faces = []

Metrial 材质指视觉上的效果，色彩、纹理、光滑度、反射率等等

```javascript
// 立方体
var geometry = new THREE.BoxGeometry(1, 1, 1);
// 材质
var material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
// 网格对象：将材质应用到几何体上。作为一个物体加入到场景中
var cube = new THREE.Mesh(geometry, material);
scene.add(cube);
camera.position.z = 5;
```

- 当调用  add(cube) 时，物体会被放到坐标系的 (0, 0, 0) 原点上，而相机也默认在这个位置。所以此处我们将相机移动到了 z = 5 这个位置

#### 渲染场景

```javascript
(function animate() {
    requestAnimationFrame(animate);
    cube.rotation.x += 0.01;
    cube.rotation.y += 0.01;
    renderer.render(scene, camera);
})();
```

- render(scene, camera)：调用这句话就可以在浏览器中渲染结果了
- 我们通过 cube.rotation 不断地旋转物体，形成一个动态的效果，所以要不断刷新屏幕，通过 requestAnimationFrame 不断调用自己实现，他会一分钟刷新 60 次
- 为什么不用 setInterval 来实现呢：requestAnimationFrame 会在切换标签页后停止运行，节省性能

#### result

```html
<html>
	<head>
		<title>My first three.js app</title>
		<style>
			body { margin: 0; }
			canvas { width: 100%; height: 100% }
		</style>
	</head>
	<body>
		<script src="three.js"></script>
		<script>
			var scene = new THREE.Scene();
			var camera = new THREE.PerspectiveCamera( 75, window.innerWidth/window.innerHeight, 0.1, 1000 );

			var renderer = new THREE.WebGLRenderer();
			renderer.setSize( window.innerWidth, window.innerHeight );
			document.body.appendChild( renderer.domElement );

			var geometry = new THREE.BoxGeometry( 1, 1, 1 );
			var material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );
			var cube = new THREE.Mesh( geometry, material );
			scene.add( cube );

			camera.position.z = 5;

			var animate = function () {
				requestAnimationFrame( animate );

				cube.rotation.x += 0.01;
				cube.rotation.y += 0.01;

				renderer.render( scene, camera );
			};

			animate();
		</script>
	</body>
</html>
```

### 点、线、面

在 three.js 的三维世界中，都是由点组成的，两个点连成直线，三个点连成三角形，三角形构成物体

#### 坐标系

threejs 使用右手坐标系，即 x 轴向右，y 轴向上，z 轴从屏幕指向你

#### 点

先看 threejs 对点函数的定义：

```javascript
function Vector3( x, y, z ) {

    this.x = x || 0;
    this.y = y || 0;
    this.z = z || 0;

}
```

可以看到如果不传入坐标，那么默认坐标（0，0，0），现在创建一个点：

```javascript
var point1 = new THREE.Vector3(1, 2, 3);

var point2 = new THREE.Vector3();
point2.set(1, 2, 4);
```

#### 线

我们知道两点确定一条直线，下面就来绘制线

First we need to set up the scene, camera and renderer.

```javascript
// 创建场景
var scene = new THREE.Scene();

// 创建透视相机
var camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 1, 500);
// 设置相机位置
camera.position.set(0, 0, 100);
// 镜头看向哪里
camera.lookAt(0, 0, 0);

// 创建渲染器(抗锯齿)
var renderer = new THREE.WebGLRenderer({ antialias: true });
// 设置渲染范围
renderer.setSize(window.innerWidth, window.innerHeight);
// 渲染在 dom 上
document.body.appendChild(renderer.domElement);
```

Next thing we will do is define a metrial. For line we have to use **LineBasicMaterial** or **LineDashedMaterial**.

```javascript
// 材质
var material = new THREE.LineBasicMaterial({ color: 0x00ff00 });
```

LineBasicMaterial() 有多个参数，不只 color 一个：

- color：线条的颜色，默认是白色
- linewidth：线条的宽度，默认是 1
- linecap：线条两端的外观，默认是圆角。在线条较粗的时候才能看见
- linejoin：两条线连接点处的外观，默认是圆角
- vertexColors：是否使用顶点颜色，根据顶点颜色进行插值

After material we need a **Geometry** or **BufferGeometry** with some vertices(顶点), BufferGeometry may be more performant, but for simplicity we use Geometry.

 ```javascript
// 形状
var geometry = new THREE.Geometry();
// 顶点
geometry.vertices.push(new THREE.Vector3(-10, 0, 0));
geometry.vertices.push(new THREE.Vector3(0, 10, 0));
geometry.vertices.push(new THREE.Vector3(10, 0, 0));
 ```

Note that lines are drawn between each consecutive(连续的) pair of vertices.

Now that we have points for two lines and a material, we can put them together to form a line. 

`var line = new THREE.Line(geometry, material);`

**All that's left**(剩下的一切) is to add it to the scene and call render:

```javascript
scene.add(line);
renderer.render(scene, camera);
```

Over, open in browser.

#### 面

画一个棋盘

```javascript
var renderer, camera, scene, light, cube;
var width = window.innerWidth, height = window.innerHeight;

function initRenderer() {
    renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(width, height);
    document.body.appendChild(renderer.domElement);
    // 设置背景颜色，0xffffff 白色，1.0 表示透明度
    renderer.setClearColor(0xffffff, 1.0);
}
function initCamera() {
    // 可以看到的范围是 1-2000
    camera = new THREE.PerspectiveCamera(45, width / height, 1, 2000);
    // 相机在 z 轴的 1500 位置看向原点，所以位于原点处的物体处于 1-2000 范围内，可以看到。改变 1500 这个值，会发现物体会变大变小，很好理解
    camera.position.set(0, 0, 1500);
    camera.lookAt(0, 0, 0);
}
function initScene() {
    scene = new THREE.Scene();
}
function initObject() {
    var geometry = new THREE.Geometry();
    geometry.vertices.push(new THREE.Vector3(-500, 0, 0));
    geometry.vertices.push(new THREE.Vector3(500, 0, 0));
    for (var i = 0; i <= 20; i++) {
        // 这两点连成的线平行于 x 轴
        var line = new THREE.Line(geometry, new THREE.LineBasicMaterial({ color: 0x000000 }));
        // 这样最终会形成 21 条平行于 x 轴的线，y 值的范围是[-500, 500]
        line.position.y = (i * 50) - 500;
        scene.add(line);

        line = new THREE.Line(geometry, new THREE.LineBasicMaterial({ color: 0x000000 }));
        // 绕 z 轴旋转 90 度，此时平行于 y 轴
        line.rotation.z = 90 * Math.PI / 180;
        // 这样最终会形成 21 条平行于 y 轴的线，x 值的范围是[-500, 500]
        line.position.x = (i * 50) - 500;
        scene.add(line);
    }
}
// 立即执行函数
(function(){
    initRenderer();
    initCamera();
    initScene();
    initObject();
    renderer.render(scene, camera);
})();
```

### Stats

使用帧数（FPS：每秒刷新几次）来监视我们的 3D 应用，一般都为 60 左右，当帧数较低时，就要注意了。

```javascript
// 需要引入 stats.min.js，路径：three/examples/js/libs
stats = new Stats();
stats.setMode(1);	// 显示 FPS 页面
stats.domElement.style.position = 'absolute';
stats.domElement.style.left = '0px';
stats.domElement.style.top = '0px';
document.body.appendChild(stats.domElement);

function animate(){
    requestAnimationFrame(animate);
    ......
    stats.update();	// 更新当前帧数
}
```

### 光

有了光，就不再黑暗。光的基类 THREE.Light()。下面看实用的派生类：

####  AmbientLight：环境光

环境光是经过多次反射而来的光，无法确定其最初的方向，所以所有的物体都将表现为相同的明暗程度。他的构造函数：THREE.AmbientLight(color, intensity)，参数分别是颜色和强度(默认 1，即强度 100%)

```javascript
var light = new THREE.AmbientLight(0xff0000);
scene.add(light);
```

####  PointLight ：点光源

这种光源放出的光线来自于同一点，方向辐射四面八方，例如灯泡发出的光。

构造函数：THREE.PointLight(color, intensity, distance); distance 表示光源强度经过 distance 距离逐渐衰减至 0。默认值为 0，即光源强度不衰减。

#### SpotLight：聚光灯

聚光灯，这种光源从椎体射出，在被照射的物体上产生聚光效果。

构造函数：THREE.SpotLight(color, intensity, distance, angle, exponent); angle 表示聚光灯着色的角度，用弧度作为单位，这个角度是和光源的方向形成的角度。exponent 表示光源模型中，衰减的一个参数，越大衰减约快。 

#### 材质与光源

材质与光源是相互联系、相互衬托的关系。离开光，任何材质都是无法体现的，有了材质，才有了色彩、纹理、光滑度、透明度、反射率等。

我们向场景中加入一个立方体，为了看到立体效果，你可能要调一下 camera 的位置

```javascript
function initObject() {
    var geometry = new THREE.CubeGeometry(200, 200, 50, 4, 4);
    var material = new THREE.MeshLambertMaterial({ color: 0xff0000 });
    var cube = new THREE.Mesh(geometry, material);
    // cube.position = new THREE.Vector3(0, 0, 0);
    scene.add(cube);
}
```

- THREE.CubeGeometry(width, height, depth, widthSegments, heightSegments, depthSegments) 后面 3 个参数分别表示分段数，默认是 1。
- THREE.MeshLambertMaterial(color) 兰伯特材质，他是最常见的一种材质，是灰暗的或不光滑的表面产生均匀散射而形成的材质类型，比如一张纸。
- 我们设置他的颜色为红色，可是运行之后却是黑色，这是因为场景中没有灯光，物体不能反射到人眼中，就像在黑夜里看东西。

这时你加上一个白色的环境光，就能看见红色了：

```javascript
function initLight() {
    light = new THREE.AmbientLight(0xffffff);
    scene.add(light);
}
```

#### DirectionalLight：方向光

方向光又称平行光，是一种没有衰减的光线，类似于太阳光的效果，构造函数：THREE.DirectionalLight(color, intensity).

我们再把上面场景的环境光改为方向光：光照到的地方变红

```javascript
function initLight() {
    light = new THREE.DirectionalLight(0xffffff);
    light.position.set(0, 0, 1);
    scene.add(light);
}
```

可以不断改变灯光的位置或者光照强度来看看不同的效果。

**Tips**：当场景中有多种光时，颜色会叠加，比如绿光 + 红光 = 黄光

### Texture：贴图

在 3D 世界中，纹理就是贴图。先看看复杂的构造函数：

Texture( *image*, *mapping*, *wrapS*, *wrapT*, *magFilter*, *minFilter*, *format*, *type*, *anisotropy*, *encoding* )

- image：是一个图片类型
- mapping：纹理坐标，一张贴图的左下角坐标为(0, 0)，
- wrapS、wrapT：分别表示 x、y 轴的纹理回环方式，就是指当纹理宽度小于平面时，剩下的部分怎么贴
- magFilter、minFilter：表示过滤的方式，
- format：加载的图片格式，可以取值 THREE.RGBAFormat(默认)、GRGFormat，即是否有透明度
- type：表示存储纹理的内存的每一个字节的格式，是有符号，还是没有符号等，默认：UnsignedByteType
- anisotropy： 各向异性过滤。使用各向异性过滤能够使纹理的效果更好，但是会消耗更多的内存、CPU、GPU时间，默认值 1

```javascript
function initObject() {
    // 平面
    var geometry = new THREE.PlaneGeometry(500, 300);
    // javascript 没有加载本地图片的能力，所以该页面要放在服务器中运行
    var texture = new THREE.TextureLoader().load('q.png', function(t){
        console.log(t);	// t 也是返回的 texture
    });
    var material = new THREE.MeshBasicMaterial({ map: texture });
    var cube = new THREE.Mesh(geometry, material);
    // cube.position = new THREE.Vector3(0, 0, 0);
    scene.add(cube);
}
```

**使用 Canvas 作为纹理**：

### 导入模型

3D 模型有数百种文件格式可用，threejs 也提供了许多加载程序。例如加载 glTF 模型

首先需要引入加载文件： three/examples/js/loaders/GLTFLoader.js

```javascript
const loader = new THREE.GLTFLoader()
loader.load('obj/Horse.glb', function(gltf){
    scene.add(gltf.scene)
}, undefined, function(error){
    console.error( error )
})

// 如果物体不可见，可尝试设置位置、灯光等因素

// 将场景设为白色，便于显示
renderer.setClearColor(0xffffff)

// 添加环境光，显示出物体本身的色彩
const light = new THREE.AmbientLight(0xffffff);
scene.add(light);
```

























