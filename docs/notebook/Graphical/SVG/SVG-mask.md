要实现一个不规则闭合图形的注水功能，把我难为了好几天，今天尝试了一下 Google 搜索，发现 SVG 中有这么一个功能 **mask**(蒙版)，可把我高兴坏了

### mask 基本用法

先看一个小李子：创建一个矩形，使用一个圆形的蒙版后，只能看见一个圆形

```html
<svg width="200" height="200" xmlns="http://www.w3.org/2000/svg">
    <defs>
        <mask id="mask">
            <circle cx="50" cy="50" r="30" fill="white" />
        </mask> 
    </defs>
    <rect x="0" y="0" width="100" height="100" fill="red" mask="url(#mask)" />
</svg>
```

<svg width="100" height="100" xmlns="http://www.w3.org/2000/svg">
	<circle cx="50" cy="50" r="30" fill="red" />
</svg>

- 为了更好地组织语言，一般吧 mask 定义在 defs 标签中，当然，不这么做也可以

- 给 mask 起一个 id，在使用的地方通过 mask="url(#id)" 引用

- mask 中图形的 fill 属性表明透明度，white 白色表示透明度0(完全透明)，black 黑色表示透明度1(完全不透明)，从白色到黑色的过渡颜色分别表示不同的透明度

- 我们的例子：红色矩形添加一块蒙版后，被遮住了，蒙版里面的圆为 white(完全透明)，所以可以通过圆形这个部分看见下面的矩形，所以最终显示红色圆形

  