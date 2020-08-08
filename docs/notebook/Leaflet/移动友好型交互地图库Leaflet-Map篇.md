Map 是 Leaflet 作为一个地图库最核心的功能了，也是 Leaflet 中内容最多的部分，它用来在页面上创建地图并进行各种各样的操作，长篇大论开始了。

### 构造函数

创建一个地图对象有两种方式：

1，传入一个容器对象

```js
let mapContainer = document.querySelector("#map-container")
let map = L.map(mapContainer)
```

2，直接将容器 id 传入

```js
let map = L.map('map-container')
```

但是这样还不行，地图对象还必须有两个不可缺少的参数：`zoom` 和 `center`

同样设置 `zoom` 和 `center` 也有两种方式：

1，构造函数传入

```js
let map = L.map('map-container', {
    center: [36.52, 120.31],
    zoom: 7
})
```

2，方法传入

```js
let map = L.map('map-container').setView([36.52, 120.31], 7)
```

至此，一个最基本的地图对象才算是创建成功。

### 选项参数

上面创建一个地图对象必须要有 `center` 和 `zoom` 两个参数。其实还有很多的可选参数，他们都被赋予了一个默认值，在某些特定功能需求面前可灵活添加使用。

#### 控制选项

创建一个地图对象后，界面默认在左上角显示了缩放按钮，右下角显示了 Leaflet 标识。如果我们想将他们关闭，只需要设置 false 即可。

```js
let map = L.map('map-container', {
    center: [36.52, 120.31],
    zoom: 7,
    zoomControl: true,			// 缩放按钮
    attributionControl: true	// 属性标识
})
```

#### 交互选项

Leaflet 地图默认设置了一些交互逻辑，例如：

- 地图可拖动
- 地图可通过滚轮进行缩放

- 双击缩小地图
- 按住 Shift 键，双击来放大地图
- 按住 Shift 键，地图可缩放至鼠标拖动的矩形窗口
- 点击地图，打开的 popup 弹框自动关闭
- +/- 号缩放地图

```js
let map = L.map('map-container', {
    center: [36.52, 120.31],
    zoom: 7,
    closePopupOnClick: true,			// 点击地图关闭popup弹框
    boxZoom: true,						// 地图缩放至拖动的矩形窗口
    doubleClickZoom: true,				// 双击缩放地图
    dragging: true,						// 地图可拖动
    keyboard: true,						// 键盘控制
    scrollWheelZoom: true				// 鼠标控制
})
```

#### 地图状态选项

这些选项用来控制地图如何展示在页面上，如地图的投影方式、缩放等级控制、以及显示的图层等

```js
let map = L.map('map-container', {
    center: [36.52, 120.31],
    zoom: 7,
    crs: L.CRS.EPSG3857,	// 地图投影，默认是 web 墨卡托投影
    minZoom: *,				// 地图可以缩放的最小等级，如果未指定，则使用图层的 minZoom
    maxZoom: *,				// 同理
    layers: [],				// 添加到地图的图层数组，如果没有图层，则整个页面是灰的
    maxBounds: null,		// 地图会限制在给定的区域内
})
```

`maxBounds` 是一个经纬度的数组，需要**左下角**和**右上角**的经纬度即可，**注：**经纬度数组先写维度

```js
maxBounds: [
    [30, 120],
    [40, 160]
]
```

### 事件

Map 事件是对用户的一系列操作做出相对的反应，用户可以凭此进行下一步操作。例如图层的切换、地图状态的改变、鼠标事件等等。

给 Map 对象添加事件的方式很简单：只需要一个**事件名称**即可，然后你就可以在回调函数中获取响应的值，进行下一步操作。

```js
map.on('eventName', (evt) => {
    console.log(evt)
})
```

那 `EventName` 都有哪些取值呢？下面就一一列举

#### 交互事件

交互事件就是用户使用鼠标、键盘等与地图对象之间的交互。

事件名称与 JavaScript 中的类似，有 12 种：click、dbclick、mousedown、mouseup、mouseover、mouseout、mousemove、contextmenu、keypress、keydown、keyup、preclick

示例：显示鼠标处的经纬度

```js
map.on('mousemove', (e) => {
    console.log(e.latlng)
})
```

#### 图层事件

- baselayerchange：基本图层更换时触发
- layeradd：添加图层
- layerremove：移除图层



























