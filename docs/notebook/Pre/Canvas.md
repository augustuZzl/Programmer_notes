### 什么是 Canvas

- HTML5 的 Canvas 元素使用 JavaScript 在网页上绘制图像


- 画布是一个矩形区域，您可以控制其每一像素。


- Canvas 拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法。

- Canvas 有一个默认的高度和宽度，也可以使用标签的属性 width 和 height 来改变；注意不能使用 style 的 width 和 height 来设置宽度和高度（会拉伸画布）。

### Hello World

#### 创建 Canvas 元素

```html
<canvas id="myCanvas" width="600px" height="400px"></canvas>
```

#### 通过 JavaScript 来绘制

使用 id 找到 canvas 元素

```javascript
var canvas = document.getElementById("myCanvas");
```

创建 context 对象

```javascript
var ctx = canvas.getContext("2d");
```

绘制红色矩形：填充和笔触（描边）

```javascript
ctx.fillStyle = "#FF0000";	// 颜色红色
ctx.fillRect(0, 0, 150, 75);	// 左上角（0， 0），长 150, 宽 75
```

```javascript
ctx.strokeStyle = "#FF0000";
ctx.strokeRect(0, 0, 150, 75);	// 笔触即描边
```

### 实例

#### 绘制线条

```javascript
ctx.moveTo(10, 10);
ctx.lineTo(150, 50);
ctx.lineTo(10, 50);
ctx.stroke();
```

#### 绘制圆形

```javascript
ctx.fillStyle = "#FF0000";
ctx.beginPath();
ctx.arc(100, 100, 20, 0, Math.PI * 2, true);
ctx.stroke();
```

- arc(圆心x，圆心y，半径r，起始弧度，终止弧度，顺时针/逆时针)

#### 贝瑟尔曲线

```javascript
ctx.beginPath();
ctx.moveTo(100, 100);
ctx.bezierCurveTo(150, 50, 250, 450, 300, 300);
ctx.stroke();
```

#### 绘制渐变色

```javascript
var grd = ctx.createLinearGradient(0, 0, 175, 50);
grd.addColorStop(0, "#FF0000");
grd.addColorStop(1, "#00FF00");
ctx.fillStyle = grd;
ctx.fillRect(0, 0, 175, 50);
```

#### 绘制图像

```javascript
var image = new Image();
image.src = "ct_html5_canvas_image.gif";
// 放在 onload 方法中，确保图片加载完毕才能绘图
image.onload = function(){
    ctx.drawImage(image, 0, 0); // x, y
    // 相当于把图片放在画布左上角（0，0）宽50高50的区域内
    ctx.drawImage(image, 0, 0, 100, 50); // x, y, width, height;
    // 将图片上左上角（20,45）宽29高54的区域放在画布左上角（100， 100）宽50高50的区域内
    ctx.drawImage(image, 20, 45, 20, 54， 100, 100, 50, 50); 
}
```



### 运动

画上屏幕的元素是不能改变的，所以要运动，需要重画才能实现，与 dom 操作改变属性值完全不一样。

即：每帧重绘：清屏 -> 绘制

```javascript
var x = 100;
// 不显示标签页后就停止运行
setInterval(function(){
    x++;
    // 清除此区域内的元素
    ctx.clearRect(0, 0, 600, 400);
    // 画图形
    ctx.beginPath();
    ctx.arc(x, 100, 50, 0, Math.PI * 2, true);
    ctx.closePath();
    ctx.fillStyle = "orange";
    ctx.fill();
}, 20);  
```

### 面向对象封装

```javascript
var myCanvas = document.getElementById('myCanvas');
var ctx = myCanvas.getContext('2d');
// 构造函数
function Ball(x, y, r, speed){
    this.x = x;
    this.y = y;
    this.r = r;
    this.speed = speed;
}
var ball1 = new Ball(100, 100, 50, 1);
var ball2 = new Ball(100, 200, 50, 2);
// 渲染函数
Ball.prototype.render = function(){
    ctx.beginPath();
    ctx.arc(this.x, this.y, this.r, 0, Math.PI * 2, true);
    ctx.closePath();
    ctx.fill();
}
// 更新函数
Ball.prototype.update = function(){
    this.x += this.speed;
}
// 定时器
setInterval(function(){
    ctx.clearRect(0, 0, 600, 400);
    ball1.render();
    ball1.update();
    ball2.render();
    ball2.update();
}, 20);
```

### 实战：炫彩小球

鼠标移动，出现五彩斑斓的小球：

<iframe src="https://augustuzzl.github.io/code/canvas-balls.html" width="100%" height="420px"></iframe>
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Canvas</title>
    <style>
        html, body {
            width: 100%;
            height: 100%;
            padding: 0;
            margin: 0;
            background-color: black;
        }
    </style>
</head>
<body>
    <canvas id="myCanvas"></canvas>
    <script>
        var myCanvas = document.getElementById('myCanvas');
        var ctx = myCanvas.getContext('2d');
        // 通过 js 设置宽高
        myCanvas.width = document.documentElement.clientWidth;
        myCanvas.height = document.documentElement.clientHeight;

        // 保存所有的球
        var balls = [];

        function Ball(x, y) {
            this.x = x;
            this.y = y;
            this.r = 40;
            this.dx = parseInt(Math.random() * 8) - 4;
            this.dy = parseInt(Math.random() * 8) - 4;
            this.color = 'rgb(' + parseInt(Math.random() * 256) + ',' + 
                                parseInt(Math.random() * 256) + ',' + 
                                parseInt(Math.random() * 256) + ')';
            // 将自己添加到数组中
            balls.push(this);
        }
        // 渲染函数
        Ball.prototype.render = function(){
            ctx.beginPath();
            ctx.arc(this.x, this.y, this.r, 0, Math.PI * 2, true);
            ctx.closePath();
            ctx.fillStyle = this.color;
            ctx.fill();
        }
        // 更新函数
        Ball.prototype.update = function(){
            this.x += this.dx;
            this.y += this.dy;
            this.r --;
            // 如果半径 < 0 ,清除此圆
            if(this.r < 0){
                this.goDie();
            }
        }
        // 消亡函数(从数组中删除自己)
        Ball.prototype.goDie = function(){
            for(var i = 0; i < balls.length; i++){
                if(balls[i] === this){
                    balls.splice(i, 1); // 看看底层
                }
            }
        }
        setInterval(function(){
            // 清屏
            ctx.clearRect(0, 0, myCanvas.width, myCanvas.height);
            // 刷新所有的球
            for(var i = 0; i < balls.length; i++){
                balls[i].render();
                // 如果存在就更新
                balls[i] && balls[i].update();
            }
        }, 20);

        myCanvas.onmousemove = function(evt){
            new Ball(evt.clientX, evt.clientY);
        }

    </script>
</body>
</html>
```

