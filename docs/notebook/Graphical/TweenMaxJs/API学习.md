### 动画结构

#### TweenMax()

用来构造一个 TweenMax 对象：`TweenMax(target, duration, vars)`

- target：需要缓动的对象，可以是对象数组
- duration：持续时间，单位秒
- vars：动画参数(CSS属性、延迟、重复次数等)

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

- target：需要缓动的对象，可以是对象数组

- duration：持续时间，单位秒
- vars：动画参数(CSS属性、延迟、重复次数等)

以下两种方式效果相同：

```javascript
TweenMax.to('div', 5, { x:300 })
new TweenMax('div', 5, { x:300 })
```

可以同时指定多个对象运动：

```javascript
TweenMax.to([obj1, obj2, obj3], 5, { x:100 })
```

同样，可以让多个对象运动不同的数值

```javascript
TweenMax.to([obj1, obj2, obj3], 5, {
    // index 表示第几个，target 代表对象
    x: function(index, target) {
        console.log(index, target)
        // 第一个对象x:100,第二个对象x:200,第三个对象x:300
        return (index + 1) * 100
    }
})
```

#### TweenMax.from()

设置动画起点来初始化一个对象，动画的终点就是初始位置：

`TweenMax.from(target, duration, vars)`

- target：需要缓动的对象，可以是对象数组
- duration：持续时间，单位秒
- vars：动画参数(CSS属性、延迟、重复次数等)

例子：物体从 x=500 回到原先位置

```html
<div style="width:100px;height:100px;background:red;"></div>
<script>
	TweenMax.from('div', 5, { x:500 })
</script>
```

#### TweenMax.fromTo()

设置动画起点和终点来初始化一个对象：`TweenMax.fromTo(target, duration, fromVars, toVars)`

- target：需要缓动的对象，可以是对象数组
- duration：持续时间，单位秒
- fromVars：起始点动画参数
- toVars：结束点动画参数

```javascript
TweenMax.fromTo('div', 5, { x: 100 }, { x:500 })
```

#### TweenMax.staggerTo()

为多个目标制作一个有间隔的动画序列，相当于多个 TweenMax 的数组

`TweenMax.staggerTo(targets, duration, vars, stagger, onCompleteAll, onCompleteAllParams, onCompleteAllScope)`

- targets：需要缓动的对象，可以是对象数组
- duration：动画的秒数，如果设置了 useFrames:true 那么就为帧数
- vars：动画的参数
- stagger：每个动画的起始间隔，默认为 0，即同时启动
- onCompleteAll：当所有动画都完成后调用的函数
- onCompleteAllParams：onCompleteAll 函数的参数，以数组的形式传入
-  onCompleteAllScope：onCompleteAll 函数的作用域，this

如下：第一个 obj 运动开始 0.5s 后第二个 obj 开始动画，以此类推

```javascript
TweenMax.staggerTo(".box", 1, { rotation:360, y:100 }, 0.5);
```

































