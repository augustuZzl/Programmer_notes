### 什么是 Canvas

- HTML5 的 Canvas 元素使用 JavaScript 在网页上绘制图像

- 画布是一个矩形区域，您可以控制其每一像素。

- Canvas 拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法。

- Canvas 有一个默认的高度和宽度，也可以使用标签的属性 width 和 height 来改变；注意不能使用 style 的 width 和 height 来设置宽度和高度（相当于拉伸画布，变虚）。

### Hello World

#### 创建 Canvas 元素

```html
<canvas id="myCanvas" width="600px" height="400px">
    如果浏览器不支持 Canvas, 则显示这条文本
</canvas>
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
// 绘制一条宽度为 20 的红线
ctx.strokeStyle = "red";
ctx.lineWidth = 20;
ctx.moveTo(100, 100);
ctx.lineTo(200, 100);
ctx.stroke();
```

再来看看下面这个代码：你会认为绘制一条宽 20 的红线和一条宽 40 的蓝线吗？

```javascript
ctx.strokeStyle = "red";
ctx.lineWidth = 20;
ctx.moveTo(100, 100);
ctx.lineTo(200, 100);
ctx.stroke();

ctx.strokeStyle = "blue";
ctx.lineWidth = 40;
ctx.moveTo(100, 200);
ctx.lineTo(200, 200);
ctx.stroke();
```

答案是否定的，最终结果是两条宽 40 的蓝线，即上一个设置被后一个覆盖了。那该如何解决呢？这就需要 beginPath() 这个函数了，我们可以把它翻译成**开始一个新的状态**，他会保存上一个状态，即红线不会被影响到了。

```javascript
ctx.strokeStyle = "red";
ctx.lineWidth = 20;
ctx.moveTo(100, 100);
ctx.lineTo(200, 100);
ctx.stroke();

ctx.beginPath();	// 开始新的状态，会把红色和线宽20继承过来，但自己可以改变值

ctx.strokeStyle = "blue";
ctx.lineWidth = 40;
ctx.moveTo(100, 200);
ctx.lineTo(200, 200);
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

### 实战：贪食蛇

点击玩耍：[贪食蛇](https://augustuzzl.github.io/code/snake.html)

```html
<script>
    var canvas = document.getElementById("myCanvas");
    var ctx = canvas.getContext("2d");

    var backMap, food, snake, snakeBody, speedX, speedY, timer, isStart;

    // 初始化各种参数
    function init(){
        // 标记欢迎页、结束页，避免在游戏时点击事件混乱
        isStart = true;
        // 存储所有的蛇（蛇可以看做一个一个的小方框）
        snakeBody = [];
        // x y 方向的速度
        speedX = -20;
        speedY = 0;
        // 循环计时器
        timer = null;
        // 背景
        backMap = new BackMap(20);
        backMap.drawMap();
        // 食物
        food = new Food(20);
        food.drawFood();
        // 蛇
        snake = new Snake();
        snake.drawSnake();
        snakeBody.push(snake);
    }

    // 键盘响应
    document.onkeydown = function(event){
        var evt = event || window.event;
        keyHandler(evt.keyCode);
    }

    // canvas 点击
    canvas.onclick = function(evt){
        var clientX = evt.clientX;
        var clientY = evt.clientY;
        if(!isStart){
            if(clientX >= 250 && clientX <= 350 && clientY >= 400 && clientY <= 440){
                // 清除
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                start();
            }
        }
    }

    // 欢迎
    hello();

    // 启动类
    function start(){
        isStart = true;
        init();
        timer = setInterval(() => {
            // 清除
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            // 蛇的移动
            var length = snakeBody.length;
            // 最后一个蛇
            var lastSnake = snakeBody[length - 1];
            // 先临时存下之前的位置
            var lastX = lastSnake.x, lastY = lastSnake.y;
            lastSnake.x = snakeBody[0].x + speedX;
            lastSnake.y = snakeBody[0].y + speedY;
            // 如果出界，结束
            if(lastSnake.x < 0 || lastSnake.x >= 600 || lastSnake.y < 0 || lastSnake.y >= 600){
                gameOver();
            }
            // 如果碰撞自身，结束
            for(var i = 0; i < length - 1; i++){
                if(lastSnake.x === snakeBody[i].x && lastSnake.y === snakeBody[i].y){
                    gameOver();
                }
            }
            // 如果与食物坐标相等
            if(lastSnake.x === food.x && lastSnake.y === food.y){
                // 增加蛇
                var newSnake = new Snake();
                newSnake.x = lastX;
                newSnake.y = lastY;
                // 移除蛇尾，插入舌头
                snakeBody.unshift(snakeBody.pop());
                // 新蛇插入蛇尾
                snakeBody.push(newSnake);
                // 食物随机换位置
                food.x = parseInt(Math.random() * 30) * 20;
                food.y = parseInt(Math.random() * 30) * 20;
            } else {
                // 移除蛇尾,插入蛇头
                snakeBody.unshift(snakeBody.pop());
            }
            // 重绘
            for(var i = 0; i < snakeBody.length; i++){
                snakeBody[i].drawSnake();
            }
            backMap.drawMap();
            food.drawFood();
        }, 600);
    }

    // 欢迎页
    function hello(){
        isStart = false;
        ctx.fillStyle = "black";
        ctx.font = "50px 微软雅黑";
        ctx.textAlign = "center";
        ctx.fillText("欢迎来玩贪食蛇", 300, 200);
        ctx.fillStyle = "green";
        ctx.fillRect(250, 400, 100, 40);
        ctx.fillStyle = "black";
        ctx.font = "30px consolas";
        ctx.fillText("Start", 300, 430);
    }

    // GameOver
    function gameOver(){
        isStart = false;
        clearInterval(timer);
        timer = null;
        ctx.fillStyle = "black";
        ctx.font = "80px 微软雅黑";
        ctx.fillText("Game Over", 300, 200);
        ctx.font = "40px consolas";
        ctx.fillText("score: " + (snakeBody.length - 1), 300, 300);
        ctx.fillStyle = "green";
        ctx.fillRect(250, 400, 100, 40);
        ctx.fillStyle = "black";
        ctx.font = "25px consolas";
        ctx.fillText("ReStart", 300, 430);
    }

    // 键盘控制类
    function keyHandler(keyCode){
        switch(keyCode){
                // 左
            case 37 : {
                if(speedX <= 0) {speedX = -20; speedY = 0;}
                break;
            }
                // 上
            case 38 : {
                if(speedY <= 0) {speedX = 0; speedY = -20;}
                break;
            }
                // 右
            case 39 : {
                if(speedX >= 0) {speedX = 20; speedY = 0;}
                break;
            }
                // 下
            case 40 : {
                if(speedY >= 0) {speedX = 0; speedY = 20;}
                break;
            }
        }
    }

    // 蛇类
    function Snake(){
        this.x = 300;
        this.y = 300;
        this.size = 20;
        this.color = "blue";
        this.drawSnake = function(){
            ctx.beginPath();
            ctx.fillStyle = this.color;
            ctx.fillRect(this.x, this.y, this.size, this.size);
            ctx.save();
        }
    }

    // 食物类
    function Food(foodSize){
        this.foodSize = foodSize;
        this.x = 200;
        this.y = 200;
        this.color = "red";
        this.drawFood = function(){
            ctx.beginPath();
            ctx.fillStyle = this.color;
            ctx.fillRect(this.x, this.y, this.foodSize, this.foodSize);
            ctx.save();
        }
    }

    // 背景类
    function BackMap(boxSize){
        // 每个小格子的宽度
        this.boxSize = boxSize;
        this.drawMap = function(){
            ctx.lineWidth = 1;
            ctx.strokeStyle = "#ddd";
            ctx.beginPath();
            for(var i = 1; i < 30; i++){
                // 竖轴
                ctx.moveTo(i * this.boxSize, 0);
                ctx.lineTo(i * this.boxSize, canvas.height);
                // 横轴
                ctx.moveTo(0, i * this.boxSize);
                ctx.lineTo(canvas.width, i * this.boxSize);
            }
            ctx.stroke();
        }
    }
    // 这样写无法访问到函数，why?
    // BackMap.prototype.drawMap = function(){
    //     ctx.moveTo(1 * this.boxSize, 0);
    //     ctx.lineTo(1 * this.boxSize, canvas.height);
    //     ctx.stroke();
    // }
</script>
```

