### 选择器

在 CSS3 中新增了多种选择器，在这里先回顾一下之前的选择器：

- 通配符选择器：*{padding: 0;}
- 元素选择器：div{width: 0;}
- 类选择器：.class{color: red;}
- ID 选择器：#id{position: fixed;}
- 后代选择器：div p{margin: 0;}

#### 子元素选择器

定义：只能选择某个元素的子元素

语法格式：父元素 > 子元素

#### 兄弟选择器

##### 相邻兄弟选择器

定义：选择紧接在某个元素后的兄弟元素

语法格式：元素 + 相邻兄弟元素

##### 通用兄弟选择器

定义：选择某个元素后的所有兄弟元素

语法格式：元素 ~ 后面所有兄弟元素

#### 群组选择器

将具有相同样式的元素分组在一起，选择器之间使用**逗号**隔开

语法格式：元素1,元素2,元素3...

#### 属性选择器

定义：选择带有指定属性的元素

语法格式：

- Element[attribute]：选择所有带有 attribute 属性的元素
- Element[attribute="value"]：选择所有 attribute="value" 的元素
- Element[attribute~="value"]：选择所有 attribute 属性的值包含**单词** "value" 的元素
  - 唯一这个比较特殊
  - 注意此处的**单词**，这里他不是指正则表达式中的 * 通配符，看下面例子就明白了
  - 比如 class 属性的值可以含有个样式：`<div class="one two"></div>`
  - 使用这个选择器：`div[class~="two"]`
- Element[attribute^="value"]：选择所有 attribute 属性的值以 "value" 开头的元素
- Element[attribute$="value"]：选择所有 attribute 属性的值以 "value" 结尾的元素
- Element[attribute*="value"]：选择所有 attribute 属性的值包含 "value" 的元素
- Element[attribute|="value"]：选择所有 attribute 属性的值以 "value" 或 "value-" 开头的元素

#### 伪类选择器

##### 动态伪类

动态伪类在用户和页面进行交互的时候才能体现出来

###### 锚点伪类

- **:link**：超链接的样式(默认下划线、蓝色)

- **:visited**：超链接访问过后的样式

###### 用户行为伪类

- **:hover**：鼠标经过样式

- **:active**：鼠标按下去时的样式

- **:focus**：聚焦时的样式

##### UI 元素状态伪类

把 **:enabled**、**:disabled**、**:checked** 称为 UI 元素状态伪类

例如可以选择表单中 input、checkbox 等的状态 

##### CSS3 结构类(:nth 选择器)

CSS3 结构类也称为 :nth 选择器

- **Element:first-child**：Element 是其父元素的首个子元素，那就选择它

  ```html
  <style>
      div:first-child {
          color: red
      }
  </style>
  <body>
      <div>	该 div 是其父元素 body 的第一个子元素
          <div>1-1</div>	红色	该 div 是其父元素 div 的第一个子元素
          <div>1-2</div>	红色
      </div>
      <div>
          <div>2-1</div>	红色	该 div 是其父元素 div 的第一个子元素
          <div>2-2</div>	黑色
      </div>
  </body>
  ```

- **Element:last-child**：与上一个正好相反

- **Element:nth-child(n)**：Element 是其父元素的第 n 个子元素

  ```css
  // 我们不仅可以使用数字来选择，还可以结合 n 来写表达式，达到更好地效果
  div:nth-child(2) {
      color: red
  }
  
  div:nth-child(odd){ 选择偶数 }
  div:nth-child(even){ 选择奇数 }
  div:nth-child(4n) { 每隔 4 个选择 }
  
  div:nth-child(5n+1) {
      color: blue
  }
  ```

- **Element:nth-last-child(n)**：与上一个正好相反

- **Element:nth-of-type(n)**：选择属于父元素的特定类型的第 n 个子元素

  看下面的例子，可以看出与 nth-child() 的区别

  ```html
  <style>
      div:nth-of-type(2) {
          color: red
      }
  </style>
  <body>
      <div>0-1</div>
      <section>
      	<div>1-1</div>
          <div>1-2</div>	红色	自己是父元素 section 的第二个 div
          <div>1-3</div>
      </section>
      <div>0-2</div>	红色	自己是父元素 body 的第二个 div
      <div>0-2</div>
  </body>
  ```

- **Element:nth-last-of-type()**：与上一个正好相反

- **Element:first-of-type()**：选择属于父元素的特定类型的首个子元素

- **Element:last-of-type()**：选择属于父元素的特定类型的最后个子元素

- **Element:only-child**：选择属于父元素的唯一子元素的每个元素

- **Element:only-of-type**：选择属于父元素的特定类型的唯一子元素的每个元素

- **Element:empty**：匹配没有子元素的元素

##### 否定选择器

语法格式：父元素:not(子元素)

##### 伪元素选择器

用于向某些选择器设置特殊效果

语法格式：元素::伪元素

- Element::first-line：选择元素的第一行文本，适用于块级元素

- Element::first-letter：选择元素的文本的第一个字符，适用于块级元素

- Element::before：在元素的内容前加入新内容

  ```css
  div::before {
      content: "我是插在前面的内容"
  }
  <div>我是div的内容</div> 
  ```
  在浏览器中显示：

  `我是插在前面的内容我是div的内容`

  在控制台的样式为：

  ```html
  <div>
  ::before
  "我是div的内容"
  </div>
  ```

- Element::after：在元素的内容后面插入新的内容，多用于**清除浮动**

  ```html
  <style>
      header {
          background: "red";
      }
      header::after {
          display: block;
          content: "";
          clear: both
      }
      header > article {
          width: 40%;
          height: 30px;
          float: left;
          background: "green";
      }
      header > aside {
          width: "40%";
          height: "50px";
          float: right;
          background: "blue";
      }
  </style>
  <header>
  	<article></article>
      <aside></aside>
  </header>
  ```

- Element::selection：选中文本后的背景色和前景色

  选中文本后，默认样式是蓝底白字，使用这个选择器进行修改

  ```html
  <style>
      div::selection {
          color: "red";
          background: "green"
      }
  </style>
  <div>asdsdasfasf</div>
  ```

#### CSS 权重

很多规则应用到某个元素上时，权重决定规则优先级

权值：行内样式(1000) > ID选择器(100) > 类、属性选择器和伪类选择器(10) > 元素和伪元素(1) > *(0)

```css
// 权重：1 + 1 = 2
selection > div {
    color: red
}
// 权重：1
div {
    color: red
}
```

### 边框与圆角

#### 圆角

border-radius：可以指定 4 个值为边框添加圆角

- border-top-left-radius：左上角
- border-top-right-radius：右上角
- border-bottom-right-radius：右下角
- border-bottom-left-radius：左下角

不一定必须取 4 个值：

- 4 个值：左上角、右上角、右下角、左下角
- 3 个值：左上角、右上角和左下角、右下角
- 2 个值：左上角和右下角、右上角和左下角
- 一个值：4 个角值相同
- 如果取值百分比，那么根据长、宽设置圆角值

```html
<style>
    div {
        width: 200px;
        height: 100px;
        border: 1px solid red;
        border-radius: 50px 40% 5em 5rem;
    }
</style>
<div></div>
```

<div style="width: 200px;height: 100px;border: 1px solid red;border-radius: 50px 40% 5em 5rem;"></div>

#### 盒阴影

box-shadow：可以设置一个或多个下拉阴影的框，是一个复合属性

语法格式：box-shadow:h-shadow v-shadow blur spread color inset;

6 个参数语义：水平方向偏移量、竖直方向偏移量、模糊度、扩展、颜色、外阴影或内阴影

```html
<style>
    div {
        width: 200px;
        height: 100px;
        background: red;
        box-shadow: 20px 10px 5px 5px yellow;
    }
</style>
<div>asd</div>
```

- 模糊度：从边框向两端模糊 5px 的距离
- 扩展：从边框向两端多出 5px 的距离

<div style="width: 200px;height: 100px;background: red;box-shadow: 20px 10px 5px 5px yellow;">asd</div>

再看内阴影效果：

`box-shadow: 20px 10px 5px 5px yellow inset;`

<div style="width: 200px;height: 100px;background: red;box-shadow: 20px 10px 5px 5px yellow inset;">asd</div>

#### 边界图片

border-image：也是一个复合属性

语法格式：border-image:source slice width outset repeat;

5 个参数语义：

- border-image-source:none|url()	：图片地址
- border-image-slice:number|%|fill    ：图像边界向内偏移的距离
- border-image-width:number|%|auto    ：图像边界的宽度
- border-image-outset:length|number    ：在边框外部绘制 border-image-area 的量
- border-image-repeat:stretch|repeat|round|initial|inherit    ：重复

```html
<style>
    div {
        width: 200px;
        height: 100px;
        border: 50px solid;
        border-image: url("") 
    }
</style>
<div></div>
```

### 背景图像

- background-clip：指定背景绘制区域

  语法格式：background-clip:border-box|padding-box|content-box;

  3 个参数语义：图片分别从border、padding、content 开始显示

- background-origin：指定 background-position 是相对位置

  语法格式：background-origin：padding-box|border-box|content-box;

  3 个参数语义：偏移量相对于border、padding、content 偏移

- background-size：背景图片的大小

  语法格式：background-size：length|percentage|cover|contain;

  - contain：显示整张图片，不会拉伸图片
  - cover：等比例填充容器，不会拉伸图片
  - 200px 100px：拉伸图片宽度 200px，高度 100px
  - 200px：拉伸图片宽度为 200px
  - 100%：拉伸图片至容器的宽度
  - 100% 100%：拉伸图片至容器的宽度和高度

- background-image：背景图像

  语法格式：允许使用多个背景图像，background-image:url(img1.png),url(img2.png);

  前面的图像会被放在上面

- background：属性整合

  语法格式：background:color position size repeat origin clip attachment image;

  - attachment：图片的跟随方式，例如 fixed 等

  ```css
  /* 推荐写法 */
  div {
      bcakground: #abcdef url(img1.png) no-repeat center center;
      background-size: 50%;
      background-origin: content-box;
      background-clip: content-box;
      background-attachment: fixed;
  }
  ```

### 渐变

渐变(gradients)可以在两个或多个指定的颜色之间显示平稳的过渡

#### 线性渐变

沿着一根轴线改变颜色，从起点到终点颜色进行顺序渐变

语法格式：background:linear-gradient(direction, color-stop1, color-stop2, ...);

线性渐变有以下几种渐变方式：

##### 从上到下(默认)

background:linear-gradient(color-stop1, color-stop2, ...);

```html
<style>
    div {
        width: 200px;
        height: 100px;
        background:linear-gradient(red, green, blue)
    }
</style>
<div></div>
```

<div style="width: 200px;height: 100px;background:linear-gradient(red, green, blue)"></div>
##### 从左到右

background:linear-gradient(to end-direction, color-stop1, color-stop2, ...);

```html
<style>
    div {
        width: 200px;
        height: 100px;
        background:linear-gradient(to right, red, green, blue)
    }
</style>
<div></div>
```

<div style="width: 200px;height: 100px;background:linear-gradient(to right, red, green, blue)"></div>
##### 对角方向

background:linear-gradient(to end-level end-vertical, color-stop1, color-stop2, ...);

```html
<style>
    div {
        width: 200px;
        height: 100px;
        background:linear-gradient(to right bottom, red, green, blue)
    }
</style>
<div></div>
```

<div style="width: 200px;height: 100px;background:linear-gradient(to right bottom, red, green, blue)"></div>
##### 角度

background:linear-gradient(angle, color-stop1, color-stop2,...);



如图：

- 0deg 表示从下往上渐变
- 45 deg 表示从左下角往右上角渐变
- 90 deg 表示从左往右渐变



<div style="width: 200px;height: 100px;background:linear-gradient(45deg, red, green, blue)"></div>
##### 多颜色控制

控制每种颜色的比例

background:linear-gradient(angle, color1 length|%, color2 length|%, ...);

以下例子：20%位置处为红色、30%位置处为绿色、100%位置为蓝色

```html
<style>
    div {
        width: 200px;
        height: 100px;
        background:linear-gradient(90deg, red 20%, green 30%, blue 100%);
    }
</style>
<div></div>
```

<div style="width: 200px;height: 100px;background:linear-gradient(90deg, red 20%, green 30%, blue 100%);"></div>
##### 重复渐变

background:repeating-linear-gradient(color1 length|%, color2 length|%, ...);

```html
<style>
    div {
        width: 200px;
        height: 100px;
        background:repeating-linear-gradient(90deg, red 0%, green 20%, red 40%);
    }
</style>
<div></div>
```

<div style="width: 200px;height: 100px;background:repeating-linear-gradient(90deg, red 0%, green 20%, red 40%);"></div>
#### 径向渐变

从起点到终点颜色从内到外进行圆形渐变，用法与线性渐变类似

语法格式：background:redial-gradient(center, shape, size, color1, color2, ...);

##### 默认样式

默认形状为椭圆形

```html
<style>
    div {
        width: 200px;
        height: 100px;
        background:radial-gradient(red, green, blue);
    }
</style>
<div></div>
```

<div style="width: 200px;height: 100px;background:radial-gradient(red, green, blue);"></div>
##### 颜色控制

```html
<style>
    div {
        width: 200px;
        height: 100px;
        background:radial-gradient(red 30%, green 70%);
    }
</style>
<div></div>
```

<div style="width: 200px;height: 100px;background:radial-gradient(red 30%, green 70%);"></div>
##### 圆形

默认为椭圆形，还可以修改为圆形

```html
<style>
    div {
        width: 200px;
        height: 100px;
        background:radial-gradient(circle, red, green, blue);
    }
</style>
<div></div>
```

<div style="width: 200px;height: 100px;background:radial-gradient(circle, red, green, blue);"></div>
##### 尺寸大小

语法：background:radius-gradient(size, color1, color2, ...);

size 取以下 4 个值：

- closest-side：最近边
- closest-corner：最近角
- farthest-side：最远边
- farthest-corner：最远角

```html
此代码不通
<div style="
     width: 200px;
     height: 100px;
     background:radial-gradient(30% 70%, circle, closest-side, red, green);
     ">
</div>
```

### 文本

#### 文本阴影

语法格式：text-shadow:h-shadow v-shadow blur color;

参数释义：水平偏移 竖直偏移 模糊距离 颜色

```html
<style>
    h1 {
        text-shadow: 5px 5px 2px red;
    }
</style>

<h1>text-shadow</h1>
```

<h1 style="text-shadow: 5px 5px 2px red;">text-shadow</h1>

#### 文本换行

对于英文，浏览器会根据容器尺寸，选择在半角空格或连字符处换行；

对于中文，浏览器会在文字或标点符号处换行；

例如：

```html
<div style="width:200px;">
    Whatever is worth doing is worth doing well.任何值得做的，就把它做好。
</div>
```

<div style="width:200px;border:1px solid;">
    Whatever is worth doing is worth doing well.任何值得做的，就把它做好。
</div>

但是看下面这种情况：

```html
<div style="width:200px;">
    请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
</div>
```

<div style="width:200px;border:1px solid;">
    请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
</div>

浏览器的宽度无法放下整个单词或者链接，那么就会撑破容器，当然我们可以使用 overflow 来处理：

1，隐藏超出部分：

```html
<div style="width:200px; overflow:hidden;">
    请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
</div>
```

<div style="width:200px;overflow:hidden;border:1px solid;">
    请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
</div>

2，添加滚动条：

```html
<div style="width:200px; overflow:auto;">
    请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
</div>
```

<div style="width:200px;overflow:auto;border:1px solid;">
    请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
</div>

现在来看我们的主角：3 种换行属性

- word-wrap
- word-break
- word-space

##### word-wrap

word-wrap 可以实现断词换行，有两种取值：normal、break-word

- normal：与浏览器原先处理方式相同，相当于没有设置
- break-word：允许断词换行

对我们上面的长单词例子添加此属性：

```html
<div style="width:200px;word-wrap:break-word;">
    请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
</div>
```

<div style="width:200px;border:1px solid;word-wrap:break-word;">
    请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
</div>

执行过程：到【这个单词:】处，发现后面的单词无法再此行容下，在标点符号【:】处换行，因此第一行出现大量空白，长单词到了第二行发现仍然容不下，于是应用 break-word 来断词换行，因此长单词被截断了，不在撑破容器。

##### word-break

word-break 可以设置浏览器自动换行的方式，有 3 中取值：normal、break-all、keep-all

- normal：等于没设

- break-all：将浏览器的换行方式设为根据容器尺寸允许断词换行，那与上面的 word-wrap:break-word 有什么区别呢？

  还是那上面的那个例子，看看区别：

  ```html
  <div style="width:200px;word-break:break-all;">
      请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
  </div>
  ```

  <div style="width:200px;border:1px solid;word-break:break-all;">
      请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
  </div>

  将两个放在一起直观看看：左边 word-wrap:break-word，右边 word-break:break-all

  <div style="width:500px;height:150px;">
      <div style="width:200px;border:1px solid;word-wrap:break-word;float:left;">
          请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
      </div>
      <div style="width:200px;border:1px solid;word-break:break-all;float:right;">
          请看看这个单词：abcdefghigklmnopqresuvwxyz；请访问这个链接：https://augustuzzl.github.io/Programmer_notes/
      </div>
  </div>

  很明显的看出区别：左边是沿用了浏览器的默认换行方式，因此第一行出现大量空白，而右边是改变浏览器的换行方式，根据容器尺寸换行，忽略了标点符号

- keep-all：不允许断词，在英文的情况下等同于 normal，在中文的情况下：只会在标点符号处换行

  ```html
  <div style="width:200px;word-break:keep-all;">
      你说为什么这句话说了这么久还没说完呢？我不知道。
  </div>
  ```

  <div style="width:200px;border:1px solid;word-break:keep-all;">
      你说为什么这句话说了这么久还没说完呢？我不知道。
  </div>

##### white-space

用来设置空白符和换行符，可以取值 normal、pre、nowrap、pre-line、pre-wrap

- normal：会忽略多余空白符和换行符
- pre：会保留空白符和换行符，相当于 `<pre>` 标签
- nowrap：不会自动换行，当使用 text-overflow 属性时需要配合 white-space:nowrap 和 overflow:hidden 才起作用
- pre-line：忽略多余空白符，但会保留换行符，会自动换行
- pre-wrap：与 pre-line 区别是会保留多余空白符

##### 总结

标签里展示源代码时，遇到有 url 属性时会很长，导致撑破页面（尤其是移动端），可以用white-space: pre-wrap 加上 word-wrap: break-word;

标签外常见的强制换行方式是 overflow:hidden; 加上 word-wrap: break-word;


强制不换行可以 white-space: nowrap; 加上 word-break: keep-all;

#### 字体 @font-face

@font-face 有 4 个属性

- font-family：引用的字体
- src：字体的存放路径
- [font-weight]：粗体等
- [font-style]：斜体等

```html
<style>
    @font-face {
        font-family: 'myFont';
        src: url('myFont.eot');
    }
    h1 {
        font-family: 'muFont';
    }
</style>
```

























































