### 层模型

#### 层布局模型

层布局模型就像是图像软件 ps 中非常流行的图层编辑功能一样，每个图层能够精确定位操作，但在网页设计领域，由于网页大小的活动性，层布局没能受到热捧。但是在网页上局部使用层布局还是有其方便之处的。下面我们来学习一下 html 中的层布局。

如何让 html 元素在网页中精确定位，就像图像软件 ps 中的图层一样可以对每个图层能够精确定位操作。CSS 定义了一组定位（positioning）属性来支持层布局模型。

层模型有三种形式：
1、绝对定位(position: absolute)
2、相对定位(position: relative)
3、固定定位(position: fixed)

#### 绝对定位
如果想为元素设置层模型中的绝对定位，需要设置 position:absolute，这条语句的作用将元素从文档流中拖出来，然后使用 left、right、top、bottom 属性相对于其最接近的一个具有定位属性的父包含块进行绝对定位。如果不存在这样的包含块，则相对于 body 元素，即相对于浏览器窗口。

如下面代码可以实现 div 元素相对于浏览器窗口向右移动 100px，向下移动 50px：

```css
div{
    width:200px;
    height:200px;
    border:2px red solid;
    position:absolute;
    left:100px;
    top:50px;
}
```

#### 相对定位
如果想为元素设置层模型中的相对定位，需要设置 position:relative（表示相对定位），它通过left、right、top、bottom 属性确定元素在正常文档流中的偏移位置。相对定位完成的过程是首先按 static(float) 方式生成一个元素(并且元素像层一样浮动了起来)，然后相对于以前的位置移动，移动的方向和幅度由 left、right、top、bottom 属性确定，偏移前的位置保留不动。

如下代码实现相对于以前位置向下移动 50px，向右移动 100px;

```css
#div1{
    width:200px;
    height:200px;
    border:2px red solid;
    position:relative;
    left:100px;
    top:50px;
}
```

什么叫做“偏移前的位置保留不动”呢？

大家可以做一个实验，在 div 标签的后面加入一个 span 标签，并在span标签中写入一些文字。如下代码：

```html
<body>
    <div id="div1"></div>
    <span>偏移前的位置还保留不动，覆盖不了前面的div没有偏移前的位置</span>
</body>
```


可以明显的看出，虽然div元素相对于以前的位置产生了偏移，但是div元素以前的位置还是保留着，所以后面的span元素是显示在了div元素以前位置的后面。

#### 固定定位
fixed：表示固定定位，与 absolute 定位类型类似，但它的相对移动的坐标是视图（屏幕内的网页窗口）本身。由于视图本身是固定的，它不会随浏览器窗口的滚动条滚动而变化，除非你在屏幕中移动浏览器窗口的屏幕位置，或改变浏览器窗口的显示大小，因此固定定位的元素会始终位于浏览器窗口内视图的某个位置，不会受文档流动影响，这与 background-attachment:fixed; 属性功能相同。以下代码可以实现相对于浏览器视图向右移动 100px，向下移动 50px。并且拖动滚动条时位置固定不变。

```css
#div1{
    width:200px;
    height:200px;
    border:2px red solid;
    position:fixed;
    left:100px;
    top:50px;
}

```

#### Relative 与 Absolute 组合使用
小伙伴们学习了绝对定位的方法：使用 position:absolute 可以实现被设置元素相对于浏览器（body）设置定位以后，大家有没有想过可不可以相对于其它元素进行定位呢？答案是肯定的，当然可以。使用 position:relative 来帮忙，但是必须遵守下面规范：

```css
1、参照定位的元素必须是相对定位元素的前辈元素：
<div id="box1"><!--参照定位的元素-->
    <div id="box2">相对参照元素进行定位</div><!--相对定位元素-->
</div>
从上面代码可以看出box1是box2的父元素（父元素当然也是前辈元素了）。

2、参照定位的元素必须加入position:relative;
#box1{
    width:200px;
    height:200px;
    position:relative;        
}
3、定位元素加入position:absolute，便可以使用top、bottom、left、right来进行偏移定位了。
#box2{
    position:absolute;
    top:20px;
    left:30px;         
}
这样box2就可以相对于父元素box1定位了（这里注意参照物就可以不是浏览器了，而可以自由设置了）。
```

### 盒模型简写
还记得在模型时外边距(margin)、内边距(padding)和边框(border)设置上下左右四个方向的边距是按照顺时针方向设置的：上右下左。具体应用在 margin 和 padding 的例子如下：

```css
上设置为10px、右设置为15px、下设置为12px、左设置为14px
margin: 10px 15px 12px 14px;

通常有下面三种缩写方法:
1、如果top、right、bottom、left的值相同，如下面代码：
margin:10px 10px 10px 10px;
可缩写为：
margin:10px;
2、如果top和bottom值相同、left和 right的值相同，如下面代码：
margin:10px 20px 10px 20px;
可缩写为：
margin:10px 20px;
3、如果left和right的值相同，如下面代码：
margin:10px 20px 30px 20px;
可缩写为：
margin:10px 20px 30px;

注意：padding、border的缩写方法和margin是一致的。
```

### 颜色值缩写
关于颜色的css样式也是可以缩写的，当你设置的颜色是16 进制的色彩值时，如果每两位的值相同，可以缩写一半：

```css
p{color: #336699;}
p{color: #369;}
```

### 字体缩写
网页中的字体 css 样式代码也有他自己的缩写方式，下面是给网页设置字体的代码：

```css
body{
    font-style:italic;
    font-variant:small-caps; 
    font-weight:bold; 
    font-size:12px; 
    line-height:1.5em; 
    font-family:"宋体",sans-serif;
}
```

这么多行的代码其实可以缩写为一句：

```css
body{
    font:italic  small-caps  bold  12px/1.5em  "宋体",sans-serif;
}
```

注意：
1、使用这一简写方式你至少要指定 font-size 和 font-family 属性，其他的属性(如 font-weight、font-style、font-varient、line-height)如未指定将自动使用默认值。
2、在缩写时 font-size 与 line-height 中间要加入“/”斜扛。

一般情况下因为对于中文网站，英文还是比较少的，所以下面缩写代码比较常用：

```css
body{
	font:12px/1.5em "宋体",sans-serif;
}
```

只是有字号、行间距、中文字体、英文字体设置。
### 颜色值
在网页中的颜色设置是非常重要，有字体颜色(color)、背景颜色(background-color)、边框颜色(border)等，设置颜色的方法也有很多种：

1、英文命令颜色
前面几个小节中经常用到的就是这种设置方法：p{ color:red; }

2、RGB 颜色
这个与 photoshop 中的 RGB 颜色是一致的，由 R(red)、G(green)、B(blue) 三种颜色的比例来配色：p{ color:rgb(133,45,200); }
每一项的值可以是 0~255 之间的整数，也可以是 0%~100% 的百分数：p{ color:rgb(20%,33%,25%); }

3、十六进制颜色

这种颜色设置方法是现在比较普遍使用的方法，其原理其实也是 RGB 设置，但是其每一项的值由 0-255 变成了十六进制 00-ff：p{ color:#00ffff; }

### 长度值
长度单位总结一下，目前比较常用到px(像素)、em、% 百分比，要注意其实这三种单位都是相对单位。

1、像素
像素为什么是相对单位呢？因为像素指的是显示器上的小点（CSS 规范中假设：90像素=1英寸）。实际情况是浏览器会使用显示器的实际像素值有关，在目前大多数的设计者都倾向于使用像素 px 作为单位。

2、em
就是本元素给定字体的 font-size 值，如果元素的 font-size 为 14px ，那么 1em = 14px；如果 font-size 为 18px，那么 1em = 18px。如下代码：

p{ font-size:12px; text-indent:2em; }
上面代码就是可以实现段落首行缩进 24px（也就是两个字体大小的距离）。

下面注意一个特殊情况：
但当给 font-size 设置单位为 em 时，此时计算的标准以 p 的父元素的 font-size 为基础。如下代码：

```css
<p>以这个<span>例子</span>为例。</p>

p{ font-size:14px }
span{ font-size:0.8em; }
结果 span 中的字体“例子”字体大小就为 11.2px（14 * 0.8 = 11.2px）。
```

3、百分比
p{ font-size:12px; line-height:130% }
设置行高（行间距）为字体的130%（12 * 1.3 = 15.6px）

### 水平居中

#### 行内元素
如果被设置元素为文本、图片等行内元素时，水平居中是通过给父元素设置 text-align:center 来实现的。如下代码：

```html
<body>
      <div class="txtCenter">我是文本，哈哈，我想要在父容器中水平居中显示。</div>
</body>

<style>
      div.txtCenter{
        	text-align:center;
      }
</style>
```

#### 定宽块状元素
当被设置元素为块状元素时用 text-align：center 就不起作用了，这时也分两种情况：定宽块状元素和不定宽块状元素。这一小节我们先来讲一讲定宽块状元素。

满足定宽和块状两个条件的元素是可以通过设置 “左右 margin” 值为 “auto” 来实现居中：

```html
<body>
	<div>我是定宽块状元素，哈哈，我要水平居中显示。</div>
</body>

<style>
    div{
        border:1px solid red;/*为了显示居中效果明显为 div 设置了边框*/
        width:500px;/*定宽*/
        margin:20px auto;/* margin-left 与 margin-right 设置为 auto */
    }
</style>
```

#### 不定宽块状元素
在实际工作中我们会遇到需要为“不定宽度的块状元素”设置居中，比如网页上的分页导航，因为分页的数量是不确定的，所以我们不能通过设置宽度来限制它的弹性。

不定宽度的块状元素有三种方法居中（这三种方法目前使用的都比多）：

- 加入 table 标签

  第一步：为需要设置的居中的元素外面加入一个 table 标签 ( 包括 <tbody>、<tr>、<td> )
  第二步：为这个 table 设置“左右 margin 居中”（这个和定宽块状元素的方法一样）

  ```html
  <div>
      <table>
        <tbody>
          <tr><td>
          <ul>
              <li><a href="#">1</a></li>
              <li><a href="#">2</a></li>
              <li><a href="#">3</a></li>
          </ul>
          </td></tr>
        </tbody>
      </table>
  </div>
  
  <style>
      table{
          margin:0 auto;
      }
      ul{ list-style:none; margin:0; padding:0; }
      li{ float:left; display:inline; margin-right:8px; }
  </style>
  ```

  

- 设置 display:inline 方法

  改变块级元素的 display 为 inline 类型，然后使用 text-align:center 来实现居中效果

  ```html
  <body>
      <div class="container">
          <ul>
              <li><a href="#">1</a></li>
              <li><a href="#">2</a></li>
              <li><a href="#">3</a></li>
          </ul>
      </div>
  </body>
  
  <style>
      .container{
          text-align:center;
      }
      .container ul{
          list-style:none;
          margin:0;
          padding:0;
          display:inline;
      }
      .container li{
          margin-right:8px;
          display:inline;
      }
  </style>
  ```

  这种方法相比第一种方法的优势是不用增加无语义标签，简化了标签的嵌套深度，但也存在着一些问题：它将块状元素的 display 类型改为 inline，变成了行内元素，所以少了一些功能，比如设定长度值。

- 设置 position:relative 和 left:50%

  通过给父元素设置 float，然后给父元素设置 position:relative 和 left:50%，子元素设置 position:relative 和 left:-50% 来实现水平居中

  ```html
  <body>
      <div class="container">
          <ul>
              <li><a href="#">1</a></li>
              <li><a href="#">2</a></li>
              <li><a href="#">3</a></li>
          </ul>
      </div>
  </body>
  
  <style>
      .container{
          float:left;
          position:relative;
          left:50%
      }
      .container ul{
          list-style:none;
          margin:0;
          padding:0;
  
          position:relative;
          left:-50%;
      }
      .container li{float:left;display:inline;margin-right:8px;}
  </style>
  ```

  这种方法可以保留块状元素仍以 display:block 的形式显示，优点不添加无语议表标签，不增加嵌套深度，但它的缺点是设置了 position:relative，带来了一定的副作用。

这三种方法使用得都非常广泛，各有优缺点，具体选用哪种方法，可以视具体情况而定。

### 垂直居中

#### 父元素高度确定的单行文本
父元素高度确定的单行文本的竖直居中的方法是通过设置父元素的 height 和 line-height 高度一致来实现的：

```html
<div class="container">
    hi,imooc!
</div>

<style>
    .container{
        height:100px;
        line-height:100px;
        background:#999;
    }
</style>
```

#### 父元素高度确定的多行文本
父元素高度确定的多行文本、图片、块状元素的竖直居中的方法有两种：

方法一：使用插入 table (包括tbody、tr、td)标签，同时设置 vertical-align：middle

说到竖直居中，css 中有一个用于竖直居中的属性 vertical-align，但这个样式只有在父元素为 td 或 th 时，才会生效。所以又要插入 table 标签了。下面看一下例子：

```html
<body>
    <table><tbody><tr><td class="wrap">
    <div>
        <p>看我是否可以居中。</p>
        <p>看我是否可以居中。</p>
        <p>看我是否可以居中。</p>
        <p>看我是否可以居中。</p>
        <p>看我是否可以居中。</p>
    </div>
    </td></tr></tbody></table>
</body>

table td{height:500px;background:#ccc}
```

因为 td 标签默认情况下就默认设置了 vertical-align 为 middle，所以我们不需要显式地设置了。

方法二：可以设置块级元素的 display 为 table-cell，激活 vertical-align 属性，但注意 IE6、7 并不支持这个样式。

```html
<div class="container">
    <div>
        <p>看我是否可以居中。</p>
        <p>看我是否可以居中。</p>
        <p>看我是否可以居中。</p>
        <p>看我是否可以居中。</p>
        <p>看我是否可以居中。</p>
    </div>
</div>

<style>
.container{
    height:300px;
    background:#ccc;
    display:table-cell;/*IE8以上及Chrome、Firefox*/
    vertical-align:middle;/*IE8以上及Chrome、Firefox*/
}
</style>
```

这种方法的好处是不用添加多余的无意义的标签，但缺点也很明显，它的兼容性不是很好，不兼容 IE6、7。

### 隐性改变 display 类型
有一个有趣的现象就是当为元素（不论之前是什么类型元素，display:none 除外）设置以下 2 个句之一：

```css
position : absolute
float : left 或 float:right
```


元素会自动变为以 display:inline-block 的方式显示，当然就可以设置元素的 width 和 height 了且默认宽度不占满父元素。

如下面的代码，都知道 a 标签是行内元素，所以设置它的 width 是 没有效果的，但是设置为 position:absolute 以后，就可以了

```html
<div class="container">
    <a href="#" title="">进入课程请单击这里</a>
</div>

<style>
    .container a{
        position:absolute;
        width:200px;
        background:#ccc;
    }
</style>
```



<style>
.container a{
    position:absolute;
    width:200px;
    background:#ccc;
}
</style>