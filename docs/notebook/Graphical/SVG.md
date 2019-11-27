### 位图与矢量图

位图：基于颜色的描述，放大失真

矢量图：基于数学的描述

### 什么是 SVG

- SVG 指可伸缩矢量图形 (Scalable Vector Graphics)
- SVG 用于定义用于网络的基于矢量的图形
- SVG 使用 XML 格式定义图形
- SVG 图像在放大或改变尺寸的情况下其图形质量不会有损失

### SVG 优势

- SVG 图像可通过文本编辑器来创建和修改
- SVG 图像可被搜索、索引、脚本化或压缩
- SVG 是可伸缩的
- SVG 图像可在任何的分辨率下被高质量地打印
- SVG 可在图像质量不下降的情况下被放大

### 第一个示例

建立一个 .svg 文件

```xml
<!-- XML 声明，standalone 表明此文件是否独立 -->
<!-- no 标识引用了外部文件，此处是下面这个 dtd 文件 -->
<?xml version="1.0" standalone="no"?>

<!-- 记得 dtd 文件可以提示标签 -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" 
"http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1"
xmlns="http://www.w3.org/2000/svg">

<!-- 绘制圆，stroke 标识轮廓 -->
<circle cx="100" cy="50" r="40" stroke="black"
stroke-width="2" fill="red"/>

</svg>
```

### SVG in HTML

#### svg 标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>SVG</title>
</head>
<body>
    <svg xmlns="http://www.w3.org/2000/svg" version="1.1" height="190">
        <polygon points="100,10 40,180 190,60 10,60 160,180"
        style="fill:lime;stroke:purple;stroke-width:5;fill-rule:evenodd;" />
    </svg>
</body>
</html>
```

#### embed 标签

```html
<embed src="demo.svg" width="300" height="100" />
```

#### img 标签

```html
<img src="demo.svg" />
```

### 形状

#### 矩形 rect

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">

    <rect width="300" height="100" x="20" y="20" rx="20" ry="20"
        style="stroke-width:1; stroke:white; stroke-opacity:0.9
               fill:rgb(0,0,255); fill-opacity:0.1;" />

</svg>
```

- rect 元素的 width 和 height 属性可定义矩形的高度和宽度，x 和 y 定义左侧和顶端位置
- rx 和 ry 使矩形产生圆角
- fill 属性定义矩形的填充颜色（rgb 值、颜色名或者十六进制值）、fill-opacity 属性定义透明度
- stroke-width 属性定义矩形边框的宽度、stroke 属性定义矩形边框的颜色、stroke-opacity：边框透明度

#### 圆形 circle

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">

    <circle cx="100" cy="50" r="40" stroke="black"
        stroke-width="2" fill="red" style="cursor:pointer" />

</svg>
```

- cx 和 cy 为原点坐标，r 为半径
- `style="cursor:pointer"` 鼠标移上变为手

#### 椭圆 ellipse

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">

    <ellipse cx="300" cy="150" rx="200" ry="80"
        style="fill:rgb(200,100,50); stroke:rgb(0,0,100); stroke-width:2"/>

</svg>
```

- rx 水平半径，ry 垂直半径

#### 线 line

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">

    <line x1="0" y1="0" x2="300" y2="300"
        style="stroke:rgb(99,99,99);stroke-width:2"/>

</svg>
```

- x1、y1、x2、y2 定义线的开始和结束

#### 折线 polyline

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">

    <polyline points="0,0 0,20 20,20 20,40 40,40 40,60"
        style="fill:white; stroke:red; stroke-width:2" />

</svg>
```

- points 定义每个角的坐标

#### 多边形 polygon

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">

    <polygon points="220,100 300,210 170,250"
        style="fill:#cccccc; stroke:#000000; stroke-width:1"/>

</svg>
```

#### 路径 path

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">

    <path d="M100 100 L200 200 H50 V50 Z" stroke="black" stroke-width="2"  fill="none" />
    <path d="M100 100 L200 200 h50 v50" stroke="black" stroke-width="2"  fill="none" />

</svg>
```

- H50 表示水平划线到坐标为 50 的位置、h50 表示水平划线向右50个像素

- M = moveto：起始位置
- L = lineto：后续位置
- H = horizontal lineto：水平划线
- V = vertical lineto：垂直划线
- Z = closepath：闭合

```xml
<path d="M150 150 A100 100 0 0 1 250 150 L225 175 A100 100 0 0 0 175 175Z" 
        stroke="black" stroke-width="2"  fill="none" />
```

A 标签有 7 个属性：x半径、y半径、角度、 长弧(1)或短弧(0)、方向逆时针(0)顺时针(1)、终点x、终点y

- A = elliptical Arc：弧形
- C = curveto
- S = smooth curveto
- Q = quadratic Belzier curve
- T = smooth quadratic Belzier curveto

由于绘制的复杂性，建议使用 svg 编辑器绘制复杂的图形

### 其它标签

#### g 标签

以下一组同心圆，如果要改变他们的圆心到 （200, 200），那么要一个个改变吗？

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">

    <circle cx="100" cy="100" r="50" stroke="black" stroke-width="2" fill="transparent" />
    <circle cx="100" cy="100" r="40" stroke="black" stroke-width="2" fill="transparent" />
    <circle cx="100" cy="100" r="30" stroke="black" stroke-width="2" fill="transparent" />
    <circle cx="100" cy="100" r="20" stroke="black" stroke-width="2" fill="transparent" />
    <circle cx="100" cy="100" r="10" stroke="black" stroke-width="2" fill="transparent" />

</svg>
```

使用 g 标签可以将他们的公共属性提取出来：

```xml
<g stroke="black" stroke-width="2" fill="transparent">
    <circle cx="100" cy="100" r="50" />
    <circle cx="100" cy="100" r="40" />
    <circle cx="100" cy="100" r="30" />
    <circle cx="100" cy="100" r="20" />
    <circle cx="100" cy="100" r="10" />
</g>

```

- 由于 cx 和 cy 是圆形独有的属性，其他图形不具有，所以圆心坐标无法提取出来

使用 g 标签的平移属性达到目的：

```xml
<g transform="translate(100, 100)" stroke="black" stroke-width="2" fill="transparent">
    <circle cx="100" cy="100" r="50" />
    <circle cx="100" cy="100" r="40" />
    <circle cx="100" cy="100" r="30" />
    <circle cx="100" cy="100" r="20" />
    <circle cx="100" cy="100" r="10" />
</g>
```

#### text 标签

```xml
<text x="100" y="100" font-size="20" text-anchor="middle">SVG</text>
```

- 默认 x 和 y 为文字的偏左下角坐标
- `text-anchor="middle"` 将 x y 设为文字中下角的坐标；"start"：左下角（默认）；"end"：右下角

#### image 标签

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" 
    xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">

    <image x="0" y="0" width="100" heigth="100" xlink:href="xxx.png" />

</svg>
```

- 注意添加 `xmlns:xlink="http://www.w3.org/1999/xlink"` 这个命名空间

### SVG 滤镜

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">

    <defs>
        <filter id="Gaussian_Blur">
            <feGaussianBlur in="SourceGraphic" stdDeviation="3" />
        </filter>
    </defs>

    <ellipse cx="200" cy="150" rx="70" ry="40"
        style="fill:#ff0000; stroke:#000000; stroke-width:2; filter:url(#Gaussian_Blur)"/>

</svg>
```

-  必须在  defs 标签中定义 SVG 滤镜
- filter 用来定义滤镜，id 属性来标识此滤镜
- feGaussianBlur 标签代表高斯滤镜， stdDeviation 属性可定义模糊的程度 
-  in="SourceGraphic" 这个部分定义了由整个图像创建效果 
- filter:url 标识使用哪个滤镜

滤镜大全：feBlend、feColorMatrix、feComponentTransfer、feComposite、feConvolveMatrix、feDiffuseLighting、feDisplacementMap、feFlood、feGaussianBlur、feImage、feMerge、feMorphology、feOffset、feSpecularLighting、feTile、feTurbulence、feDistantLight、fePointLight、feSpotLight

### SVG 渐变

#### 线性渐变

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">

    <defs>
        <linearGradient id="orange_red" x1="0%" y1="0%" x2="100%" y2="0%">
            <stop offset="0%" style="stop-color:rgb(255,0,0); stop-opacity:1"/>
            <stop offset="50%" style="stop-color:rgb(0,255,0); stop-opacity:1"/>
            <stop offset="100%" style="stop-color:rgb(0,0,255); stop-opacity:1"/>
        </linearGradient>
    </defs>

    <ellipse cx="200" cy="190" rx="85" ry="55" style="fill:url(#orange_red)"/>

</svg>
```

- 必须在  defs 标签中定义 SVG 渐变
- linearGradient 标签代表线性渐变，分为水平、垂直、角度渐变
-  linearGradient 标签的 x1、x2、y1、y2 属性可定义渐变的开始和结束位置，此处是水平渐变
-  渐变的颜色范围可由两种或多种颜色组成。每种颜色通过一个 stop 标签来规定。offset 属性用来定义渐变的开始位置 

#### 放射渐变

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">

    <defs>
        <radialGradient id="grey_blue" cx="50%" cy="50%" r="50%" fx="50%" fy="50%">
            <stop offset="0%" style="stop-color:rgb(255,0,0); stop-opacity:1"/>
            <stop offset="50%" style="stop-color:rgb(0,255,0); stop-opacity:1"/>
            <stop offset="100%" style="stop-color:rgb(0,0,255); stop-opacity:1"/>
        </radialGradient>
    </defs>

    <ellipse cx="230" cy="200" rx="110" ry="100" style="fill:url(#grey_blue)"/>

</svg>
```

- cx、cy、r 属性定义外圈；fx、fy 定义内圈

### JavaScript do SVG

```javascript
<script>
    window.onload = function(){
        // 创建 svg 标签
        var svgNs = "http://www.w3.org/2000/svg";
        var svg = document.createElementNS(svgNs, "svg");
        // 设置标签属性
        svg.setAttribute("xmlns", svgNs);
        svg.setAttribute("width", "100%");
        svg.setAttribute("height", "100%");
        // 添加到父标签
        var div = document.getElementById("container");
        div.appendChild(svg);
    }
</script>
```



### 实战一：关系图

SVG 标签有很多属性、样式，完全与其他 html 标签一样。下面使用 SVG 做一个兴趣图谱：

<iframe src="https://augustuzzl.github.io/code/svg-rela.html" width="100%" height="420px"></iframe>
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>兴趣图谱</title>
    <style>
        #container {
            width: 100%;
            height: 400px;
            border: black 1px solid;
        }
    </style>
</head>
<body>
    <div id="container"></div>

    <script>
        window.onload = function() {

            var svgNs = 'http://www.w3.org/2000/svg';
            // 父容器相关信息
            var container = document.getElementById('container');
            var centerX = container.offsetWidth / 2;
            var centerY = container.offsetHeight / 2;
            // 模拟数据成圆形分布
            var circleNum = 9;
            var angle = 360 / circleNum;
            var centerR = 150;
            var otherData = [];
            for(var i = 0; i < circleNum; i++){
                var y = centerR * Math.sin(i * angle * Math.PI / 180) + centerY;
                var x = centerR * Math.cos(i * angle * Math.PI / 180) + centerX;
                otherData.push({ x : x, y : y, text: i});
            }
            var data = {
                centerNode: { text : '中国'},
                otherNode: otherData
            };
            

            // 创建 svg 标签
            var svg = createTag('svg', {'xmlns':svgNs, 'width':'100%', 'height':'100%'});
            // 添加其他节点
            for(var i = 0; i < data.otherNode.length; i++){
                addOtherNode(data.otherNode[i]);
            }
            // 添加中央节点
            addCenterNode();
            // 添加 svg 标签
            container.appendChild(svg);
            // 事件
            active();
            

            // 创建标签的函数
            function createTag(tagName, attrs) {
                var oTag = document.createElementNS(svgNs, tagName);
                for(var attr in attrs) {
                    oTag.setAttribute(attr, attrs[attr]);
                }
                return oTag;
            }

            // 添加中央节点
            function addCenterNode() {
                var g = createTag('g', {'style':'cursor: pointer'});
                var circle = createTag('circle', {
                    'cx':centerX, 'cy':centerY, 'r':'40', 'stroke':'red', 'stroke-width':'1', 'fill':'white'});
                var text = createTag('text', {'x':centerX, 'y':centerY + 8, 'font-size':'20', 'text-anchor':'middle'});
                text.innerHTML = data.centerNode.text;
                
                g.appendChild(circle);
                g.appendChild(text);
                svg.appendChild(g);
            }

            // 添加其他节点
            function addOtherNode(otherNode) {
                var g = createTag('g', {'style':'cursor: pointer', 'class':'gStyle'});
                var line1 = createTag('line', {'x1':otherNode.x, 'y1':otherNode.y, 'x2':centerX, 'y2':centerY, 'stroke':'#ccc'});
                var line2 = createTag('line', {'x1':otherNode.x, 'y1':otherNode.y, 'x2':centerX, 'y2':centerY, 'stroke':'transparent', 'stroke-width':'10'});
                var rect = createTag('rect', {'x':(otherNode.x + centerX) / 2 - 10, 'y':(otherNode.y + centerY) / 2 - 10, 'width':'20', 'height':'20', 'rx':'5', 'fill':'#999'});
                var text1 = createTag('text', {'x':(otherNode.x + centerX) / 2, 'y':(otherNode.y + centerY) / 2 + 8, 'fill':'white', 'font-size':'20', 'text-anchor':'middle'});
                text1.innerHTML = '?';
                var circle = createTag('circle', {
                    'cx':otherNode.x, 'cy':otherNode.y, 'r':'30', 'stroke':'red', 'stroke-width':'1', 'fill':'white'});
                var text2 = createTag('text', {'x':otherNode.x, 'y':otherNode.y + 8, 'font-size':'20', 'text-anchor':'middle'});
                text2.innerHTML = otherNode.text;

                g.appendChild(line1);
                g.appendChild(line2);
                g.appendChild(rect);
                g.appendChild(text1);
                g.appendChild(circle);
                g.appendChild(text2);
                svg.appendChild(g);
            }

            // 鼠标事件动画
            function active(){
                var gTag = document.getElementsByClassName('gStyle');
                for(var i = 0; i < gTag.length; i++){
                    // 鼠标移入
                    gTag[i].onmouseenter = function(){
                        var circle = this.getElementsByTagName('circle')[0];
                        var line = this.getElementsByTagName('line')[0];
                        var rect = this.getElementsByTagName('rect')[0];
                        circleActive(circle, 30, 40);
                        line.setAttribute('stroke', 'green');
                        rect.setAttribute('fill', 'blue');
                    }
                    // 鼠标移开
                    gTag[i].onmouseleave = function(){
                        var circle = this.getElementsByTagName('circle')[0];
                        var line = this.getElementsByTagName('line')[0];
                        var rect = this.getElementsByTagName('rect')[0];
                        circleActive(circle, 40, 30);
                        line.setAttribute('stroke', '#ccc');
                        rect.setAttribute('fill', '#999');
                    }
                }
            }

            // 圆的缩放动画
            function circleActive(obj, start, end){
                // 此处如果使用 obj.r 得到的是属性字符串，而不是具体的值，可以通过打印看一看
                var nowR = start;  // // console.log(obj.r);
                var overR = end;
                obj.speed = 0;
                clearInterval(obj.timer);
                obj.timer = setInterval(function(){
                    obj.speed += (overR - nowR) / 6;
                    obj.speed *= 0.85;
                    if(Math.abs(overR - nowR) <= 1 && Math.abs(obj.speed) <= 1){
                        clearInterval(obj.timer);
                        obj.setAttribute('r', overR);
                    } else {
                        nowR += obj.speed;
                        obj.setAttribute('r', nowR);
                    }
                }, 30)
            }

        }
    </script>

</body>
</html>
```



### 实战二：模拟地图测距折线

模拟地图的测距功能，即画出连续的折线：左键开始，右键结束：

<iframe src="https://augustuzzl.github.io/code/svg-poly.html" width="100%" height="420px"></iframe>
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>地图测距折线</title>
    <style>
        #container {
            width: 100%;
            height: 400px;
            border: black 1px solid;
        }
    </style>
</head>
<body>
    <div id="container"></div>
    <script>
        window.onload = function(){
            var svgNs = "http://www.w3.org/2000/svg";
            var container = document.getElementById("container");
            var svg = createTag('svg', {'xmlns':svgNs, 'width':'100%', 'height':'100%'});
            container.appendChild(svg);

            var polyLine;
            var points = '';

            svg.onmousedown = function(evt){
                var x = evt.clientX - container.offsetLeft;
                var y = evt.clientY - container.offsetTop;
                if(!polyLine) {
                    polyLine = createTag('polyline', {'fill':'none', 'stroke':'red', 'stroke-width':'2'});
                    svg.appendChild(polyLine);
                    points = x + ',' + y;
                } else {
                    points += ',' + x + ',' + y;
                }
                polyLine.setAttribute('points', points);
                var circle = createTag('circle', {'cx': x, 'cy': y, 'r': '5', 'fill':'white', 'stroke':'red', 'stroke-width': '2'});
                svg.appendChild(circle);
            }
            svg.onmousemove = function(evt){
                if(polyLine){
                    var x = evt.clientX - container.offsetLeft;
                    var y = evt.clientY - container.offsetTop;
                    polyLine.setAttribute('points', points + ',' + x + ',' + y);
                }
            }
            // 鼠标右键结束绘制
            svg.oncontextmenu = function(){
                polyLine = undefined;
                points = '';
                // 阻止菜单弹出
                return false;
            }

            function createTag(tagName, attrs) {
                var oTag = document.createElementNS(svgNs, tagName);
                for(var attr in attrs) {
                    oTag.setAttribute(attr, attrs[attr]);
                }
                return oTag;
            }
        }
    </script>
</body>
</html>
```



