### 动画结构

#### TweenMax()

用来构造一个 TweenMax 对象：`TweenMax(target, duration, vars)`

- target：需要缓动的对象
- duration：持续时间，单位秒
- vars：动画参数

例子：一边向右移动 300px，一边透明度变为 0.3

```html
<div style="width:100px;height:100px;background:red;"></div>
<script>
    // 会通过 css 选择器选择对象，当然也可以传入对象
    new TweenMax('div', 5, {
        x: 300,
        alpha: 0.3
    })
</script>
```

**注意：**只能改变数值参数，如果你想过渡颜色 background: blue，那么他是完不成的

#### TweenMax.to()

创造一个从当前属性到目标属性的对象：`TweenMax.to(target, duration, vars)`









































