TweenMax.js 是 GreenSock 动画平台中的核心动画工具，是适用于移动端和现代互联网得到超高性能专业级动画插件，可用来构建补间动画(tween) ，补间意思是在起始状态和终点状态之间补全中间过程。

- [官方网站]( https://www.tweenmax.com.cn/ )

例如我们将一个 div 在 5s 内向右移动 300px：

```html
<div style="width:100px; height:100px; background:red;"></div>
<script>
    TweenMax.to('div', 5, {x:300})
</script>
```



- [API 学习](notebook/Graphical/TweenMaxJs/API学习)