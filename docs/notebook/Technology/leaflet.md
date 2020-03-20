### 显示地图

```html
<link rel="stylesheet" href="leaflet/leaflet.css">
<script src="leaflet/leaflet.js"></script>
<style>
    #map-container {
        width: 600px;
        height: 400px;
    }
</style>
<div id="map-container"></div>
<script>
    const map = L.map('map-container').setView([36.52, 120.31], 7)
    const layer = L.tileLayer('https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NXVycTA2emYycXBndHRqcmZ3N3gifQ.rJcFIG214AriISLbB6B5aw', {
        maxZoom: 18,
        attribution: 'augustu',
        id: 'mapbox.streets'
    }).addTo(map);
</script>
```

### 标记 Marker

只需要一句话

```js
const marker = L.marker([36.52,120.31]).addTo(map);
```

### 绘制图形

#### 圆形

```js
const circle = L.circle([36.52,120.31], {
    color: 'green',         // 边框颜色
    fillColor: '#f03',      // 填充颜色
    fillOpacity: 0.5,       // 透明度
    radius: 10000           // 半径
}).addTo(map);
```

#### 多边形

```js
const pointArr = [
    [36, 121],
    [37, 121],
    [36.5, 122],
    [36.5, 122.2]
]
var polygon = L.polygon(pointArr, {
    color: 'green',
    fillColor: '#f03',
    fillOpacity: 0.5
}).addTo(map);
```

### 弹框

#### 点击弹框

比如我们点击上面的标记，出现弹框

````js
const marker = L.marker([36.52,120.31]).addTo(map)
marker.bindPopup('这是个Marker')					// 点击打开
marker.bindPopup('这是个Marker').openPopup()		// 自动打开
````

#### 绘制弹框

在指定位置显示

```js
const popup = L.popup()
    .setLatLng([35,120])
    .setContent('俺是一个Popup图层')
    // .openOn(map);	// 自动打开弹框，但其他弹框会关闭
    .addTo(map);
```

### 事件

```js
map.on('click', function(e) {
    console.log(e);
    alert('纬度：' + e.latlng.lat + '\n经度：' + e.latlng.lng);
});
```

### 移动端

#### 全面屏适配

```html
<!-- 规定页面初始化大小及是否可缩放 -->
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />

<style>
    * {
        padding: 0;
        margin: 0;
    }
    html, body, #map-container {
        width: 100%;
        height: 100%;
    }
</style>
```

#### 定位

```js
m.locate({
    setView: true,
    maxZoom: 16
});

m.on('locationfound', function(e) {
  var radius = e.accuracy / 2;
  L.marker(e.latlng).addTo(m).bindPopup("你就在这个圈内");
  L.circle(e.latlng, radius).addTo(m);
});

m.on('locationerror', function(e) {
  console.log('定位出错=====>', e);
});
```

