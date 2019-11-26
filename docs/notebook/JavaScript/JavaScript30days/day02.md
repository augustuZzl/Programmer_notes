今天的挑战是实现一个钟表，即让指针根据当前时间转动，主要学习使用 css3 的变换属性

指针都是使用 div 做的，所以是水平的，即指向数字 9，而我们让指针转动，从 12 点开始最方便操作了：

指针以圆心为中心顺时针转动 90 deg。

```css
transform-origin: 100%;
transform: rotate(90deg);
transition: all 0.05s;
transition-timing-function: cubic-bezier(0.1, 2.7, 0.58, 1);
```

#### transform-origin

刚看这个属性的时候，说实话有点蒙，一开始看不明白到底是个什么意思：w3school 解释：

`transform-origin: x-axis y-axis z-axis;`

- x-axis：定义视图被置于 X 轴的何处，取值：left、right、center、length、%

- y-axis：定义视图被置于 Y 轴的何处，取值：left、right、center、length、%

- z-axis：定义视图被置于 Z 轴的何处，取值：length

其实这个属性就是设置旋转中心，配合 `transform: rotate(deg);` 使用，deg 表示度。默认以中心旋转

在一般的 2d 元素中，那么只需要两个参数就可以了，这两个参数分别表示距盒模型的左上角的距离，本例中，指针要以最右端为中心旋转，所以取值 100%(right)，y-axis 不写也是默认中心。

#### transition

transition 是一个简写属性，用于设置 4 个过渡属性：

- transition-property：操作的属性名，
- transition-duration：完成过渡所需的时间
- transition-timing-function：过渡过程中的速度曲线，例如上面的贝塞尔曲线
- transition-delay：何时开始过渡

#### 角度

各个指针根据 new Date() 来获取当前的时、分、秒，设置角度即可。要注意的是，分针要受秒针影响，时针要受分针影响：

```javascript
// 秒针每秒转 6 度
const seconds = now.getSeconds();
const secondsDegrees = ((seconds / 60) * 360) + 90;
secondHand.style.transform = `rotate(${secondsDegrees}deg)`;

// 分针每分也转 6 度
// 秒针转 60 度，分针转 6 度，所以分针多转 (seconds/60) * 6
const mins = now.getMinutes();
const minsDegrees = ((mins / 60) * 360) + ((seconds/60)*6) + 90;
minsHand.style.transform = `rotate(${minsDegrees}deg)`;

const hour = now.getHours();
const hourDegrees = ((hour / 12) * 360) + ((mins/60)*30) + 90;
hourHand.style.transform = `rotate(${hourDegrees}deg)`;
```

放在 setInterval() 中运行就行了。