Canvas 用于在网页上绘制图形

### 什么是 Canvas

HTML5 的 canvas 元素使用 JavaScript 在网页上绘制图像。

画布是一个矩形区域，您可以控制其每一像素。

canvas 拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法。

### 创建 Canvas 元素

```html
<canvas id="myCanvas" width="100%" height="100%"></canvas>
```

### 通过 JavaScript 来绘制

使用 id 找到 canvas 元素

```javascript
var canvas = document.getElementById("myCanvas");
```

创建 context 对象

```javascript
var ctx = canvas.getContext("2d");
```

绘制红色矩形

```javascript
ctx.fillStyle = "#FF0000";	// 颜色红色
ctx.fillRect(0, 0, 150, 75);	// 左上角（0， 0），长 150, 宽 75
```

### 实例

#### 绘制线条

```javascript
var canvas = document.getElementById("myCanvas");
var ctx = canvas.getContext("2d");
ctx.moveTo(10, 10);
ctx.lineTo(150, 50);
ctx.lineTo(10, 50);
ctx.stroke();
```

#### 绘制圆形

```javascript
var canvas = document.getElementById("myCanvas");
var ctx = canvas.getContext("2d");
ctx.fillStyle = "#FF0000";
ctx.beginPath();
ctx.arc(70, 18, 15, 0, Math.PI * 2, true);
ctx.closePath();
ctx.fill();
```

#### 绘制渐变色

```javascript
var canvas = document.getElementById("myCanvas");
var ctx = canvas.getContext("2d");
var grd = ctx.createLinearGradient(0, 0, 175, 50);
grd.addColorStop(0, "#FF0000");
grd.addColorStop(1, "#00FF00");
ctx.fillStyle = grd;
ctx.fillRect(0, 0, 175, 50);
```

#### 绘制图像

```javascript
var canvas = document.getElementById("myCanvas");
var ctx = canvas.getContext("2d");
var image = new Image();
image.src = "ct_html5_canvas_image.gif";
// 放在 onload 方法中，确保图片加载完毕才能绘图
image.onload = function(){
    ctx.drawImage(image, 0, 0);
}
```

