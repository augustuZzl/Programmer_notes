### WebGL

WebGL 是一种在浏览器中流畅展示 3D 模型和场景的一种技术。

浏览器实现了 OpenGL ES 规范，这套规范可以使用 JavaScript 操作显卡，使显卡渲染 3D 世界，显示在浏览器中。游戏、家居、虚拟现实、城市制图、CAD制图等等都可以做。

### Hello World

#### Canvas 容器

 为了使用 WebGL 进行 3D 渲染，首先需要一个 canvas 元素 

```html
<canvas id="myCanvas" width="600" height="400">
    你的浏览器不支持 Canvas
</canvas>
```

#### WebGL 上下文

```javascript
var canvas = document.querySelector("#myCanvas");
var ctx = canvas.getContext("webgl");
if(!ctx){
    alert("不支持 WebGL");
}
// 设置清除色为黑色
ctx.clearColor(0, 0, 0, 1);
// 用指定的黑色清除缓冲区
ctx.clear(ctx.COLOR_BUFFER_BIT);
```

canvas.getContext("webgl") 获取的是  **WebGLRenderingContext** ，有了它就可以在 Canvas 上绘制了，虽然创建的是三维空间，但也可以绘制 2D 图形。下面就绘制一个简单的没有纹理的 2D 图形，例如正方形。

#### 着色器

着色器通俗来讲就是负责记录像素点的位置和颜色。他是用  OpenGL ES Shading Language(GLSL) 编写的程序。

着色器分为**顶点着色器**和**片段着色器**。在使用他们时需要用 GLSL 来编写，并传给 WebGL，使之在 GPU 执行时编译。

##### 顶点着色器





