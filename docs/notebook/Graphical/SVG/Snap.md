### Snap.svg.js

SVG 是创建交互式、分辨率独立的矢量图形的极好方法，在任何大小的屏幕上都会很好看。而且Snap.svg.js 库使处理 svg 变得和 jQuery 处理 DOM 一样简单。

- 官方网站： http://snapsvg.io/ 
- 中文翻译网站： https://www.zhangxinxu.com/GitHub/demo-Snap.svg/demo/basic/ 

### 创建 SVG

> md，文学功底真差，名字都不会起

#### 创建 SVG 标签

可以直接使用 Snap 在一个空的 html 文件中创建 SVG

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Snap</title>
    <script src="snap.svg.js"></script>
</head>
<body>
    
    <script>
        const svg = Snap(100, 100)
    </script>
</body>
</html>
```

查看源代码，发现这句代码竟然留下了自己的足迹：

![](1.png)

#### 包裹 SVG 标签

页面中存在一个 svg 标签，也可以使用 Snap 进行封装：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Snap</title>
    <script src="snap.svg.js"></script>
</head>
<body>
    <svg height="100" width="100" xmlns="http://www.w3.org/2000/svg"></svg>
    <script>
        const svg = Snap("svg")
        console.log(svg) 
    </script>
</body>
</html>
```

查看控制台，发现 Snap 将 svg 包装成一个 Element，svg dom 元素作为其的 node

```
Element {node: svg, type: "svg", id: "svgSk3ssiq6b0", anims: {…}, _: {…}, …}
```

#### 加载外部 SVG 文件

```javascript
Snap.load('svg/lk.svg', function (outSvg) {
    const svg = Snap(outSvg.node)
}
```

由于跨域问题，加载外部文件需要在服务器环境中。



