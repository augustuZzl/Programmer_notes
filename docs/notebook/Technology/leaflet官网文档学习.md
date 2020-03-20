### Map

Map 是 leaflet 的最核心类，它用来在页面上创建一个地图并操作它。

#### 例子

```js
const map = L.map('map', {
    center: [36.52, 120.31],
    zoom: 7
})
```

你会发现有效果了，有缩放按钮以及右下角的 Leaflet 标识，还有鼠标变成了小手，但就是没有地图显示，这是因为需要另一个 Layer 类来指定地图图层才可以显示。

#### 构造函数

`L.map(el, options )`

此处的 el 可以传入一个元素的 id，例如上面我们传入 map；还可以直接传入一个元素对象

```js
const map = L.map(document.querySelector("#map"), {
    center: [36.52, 120.31],
    zoom: 7
})
```

#### options

| 选项               | type    | 默认值 | 描述                                                 |
| ------------------ | ------- | ------ | ---------------------------------------------------- |
| preferCanvas       | Boolean | false  | 是否使用 canvas 渲染路径。默认 false，使用 SVG来渲染 |
| attributionControl | Boolean | true   | 属性控制器，即右下角的属性信息                       |
| zoomControl        | Boolean | true   | 缩放控制器，即左上角的放大缩小按钮                   |
| closePopupOnClick  | Boolean | true   | 点击地图，弹出窗口关闭                               |
| boxZoom            | Boolean | true   | 按住 shift，地图缩放至拖动的矩形区域                 |
| doubleClickZoom    | Boolean | true   | 按住 shift，双击地图放大区域；直接双击地图缩小区域   |
| dragging           | Boolean | true   | 可以通过鼠标或手指拖动                               |
|                    |         |        |                                                      |
|                    |         |        |                                                      |

