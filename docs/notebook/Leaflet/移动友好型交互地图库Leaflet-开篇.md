Leaflet 是开源 JavaScript 库，用于移动友好的交互式地图。具有简单、性能好、易用等特点，无论在桌面还是移动平台都能高效运行。拥有丰富的插件、简洁的文档以及可读的源代码。 https://leafletjs.com/ 

一个入门程序可以帮助你更好地了解 Leaflet 的编码风格，你会发现它是 so easy。

#### 准备页面

在写任何代码前，都要先做好准备工作，比如创建地图容器，引入 Leaflet 库等等

- 引入 Leaflet 的 CSS 文件

你需要下载好 Leaflet 库，放到自己的项目中，然后引入：

```html
<link rel="stylesheet" href="leaflet/leaflet.css">
```

- 同理，引入 JavaScript 文件

```html
<script src="leaflet/leaflet.js"></script>
```

- 提供一个容器，用来存放地图

```html
<div id="map-container"></div>
```

- 设置容器的样式，例如我们想让地图全屏显示

```css
html, body, #map-container {
    width: 100%;
    height: 100%;
}
```

#### 初始化地图

做好上面的准备工作就可以愉快地编码了，下面我们就初始化地图，并设置他的的中心以及缩放等级，让地图的视野在山东：

```javascript
let map = L.map('map-container').setView([36.52, 120.31], 7)
```

可以看到，即时调用了 setView 方法，还是返回了 map 对象。Leaflet 中的大多数方法可以采用这种链式调用，简化更多的代码，让程序更简洁。记得 Java 中的 Stream 也是这种链式调用。

当你运行页面，你发现只有左上角的缩放按钮以及右下角的标识(在 Leaflet 中称之为属性，有点版权的意味)：Leaflet，整个界面都是灰的，那是因为缺少了图层，网络上有各种各样的在线 layer，例如 Eari、天地图、OSM、MapBox、高德等等。总体上我觉得高德和 google 地图加载快一些（与我是联通的网有关吗?）。下面就加载 google 地图吧。

```js
L.tileLayer('http://www.google.cn/maps/vt?lyrs=m@189&gl=cn&x={x}&y={y}&z={z}', {
    maxZoom: 18,
    minZoom: 2,
    attribution: "google map"
}).addTo(map)
```

地图出来了，右下角也变成了这样的形式：` Leaflet|google map`，是 attribution 这个属性起作用。

#### 添加 Marker、Circle、polygon

除了添加上面的 google 图层，还可以很方便的添加其他东西，下面我们添加一个标记，并画一个圆形和多边形。

- 先来一个标记

```js
let marker = L.marker([36.52, 120.31]).addTo(map)
```

- 在来一个半径为 50000 米的圆

```js
let circle = L.circle([36.52, 118.31], {
    color: 'red',
    fillColor: '#f03',
    fillOpacity: 0.5,
    radius: 50000
}).addTo(map)
```

- 在来一个三角形

```js
let polygon = L.polygon([
    [37.52, 121.31],
    [36.52, 122.31],
    [35.52, 121.31]
]).addTo(map)
```

即便他们类别不同，但你会发现他们的编码是如此类似：`L.type(coordinate, option).addTo(map)`，三角形没有设置 option，那么他的样式默认是蓝色的，而圆形应用了我们设置的红色。

#### 弹框

当你需要查看地图上某个元素的信息时，可以给他添加一个弹框，例如我们给上面的标记、圆和三角形添加一个弹框：

```js
marker.bindPopup("<b>Hello world!</b><br>I am a popup.").openPopup()
circle.bindPopup("I am a circle.")
polygon.bindPopup("I am a polygon.")
```

弹框只有在我们点击的时候才会出现，而 openPopup() 方法自动打开。

当然，popup 也可以作为一个独立的弹框，不依附在任何人身上：

```js
L.popup()
    .setLatLng([37.52, 120.31])
    .setContent("我是一个独立的弹框")
    .openOn(map)
```

仔细的你会发现，每打开一个弹框，上一个弹框就会自动关闭

#### 事件

地图最主要的功能就是与用户进行交互，例如点击、缩放、拖拽等等事件，

下面为地图添加一个点击事件，弹框显示此处的坐标：

```js
let popup = L.popup()
map.on("click", e => {
    popup
        .setLatLng(e.latlng)
        .setContent("您点击的位置是：" + e.latlng)
        .openOn(map)
})
```

#### 全部代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Leaflet学习</title>
    <link rel="stylesheet" href="leaflet/leaflet.css">
    <script src="leaflet/leaflet.js"></script>
    <style>
        body {
            padding: 0;
            margin: 0;
        }
        html, body, #map-container {
            width: 100%;
            height: 100%;
        }
    </style>
</head>
<body>
    <div id="map-container"></div>
    <script>
        let map = L.map('map-container').setView([36.52, 120.31], 7)
        L.tileLayer('http://www.google.cn/maps/vt?lyrs=m@189&gl=cn&x={x}&y={y}&z={z}', {
            maxZoom: 18,
            minZoom: 2,
            attribution: "google map"
        }).addTo(map)

        let marker = L.marker([36.52, 120.31]).addTo(map)
        let circle = L.circle([36.52, 118.31], {
            color: 'red',
            fillColor: '#f03',
            fillOpacity: 0.5,
            radius: 50000
        }).addTo(map)
        let polygon = L.polygon([
            [37.52, 121.31],
            [36.52, 122.31],
            [35.52, 121.31]
        ]).addTo(map)

        marker.bindPopup("<b>Hello world!</b><br>I am a popup.").openPopup()
        circle.bindPopup("I am a circle.")
        polygon.bindPopup("I am a polygon.")

        L.popup()
            .setLatLng([37.52, 120.31])
            .setContent("我是一个独立的弹框")
            .openOn(map)

        let popup = L.popup()
        map.on("click", e => {
            popup
                .setLatLng(e.latlng)
                .setContent("您点击的位置是：" + e.latlng)
                .openOn(map)
        })
    </script>
</body>
</html>
```

#### 总结

这个例子是 Leaflet 作为一个地图库最基本的功能，学会了这个例子，也就掌握了 Leaflet 的编码套路，等以后在其他需求面前，无非就以下 2 步：

- 打开官网文档： https://leafletjs.com/reference-1.6.0.html 
- 查看相关类的**构造函数**、**参数**、**方法**

 













