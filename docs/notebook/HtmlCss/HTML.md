HTML 是网页内容的载体，内容就是网页制作者放在页面上的想要用户浏览的信息，可以包括文字、图片、视频等

打开 Vs Code，新建一个 HTML 页面，输入 !，按回车，一个 HTML 模板就呈现在眼前了：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    
</body>
</html>
```

### `<!DOCTYPE html>`

用来声明文档类型，DOCTYPE 就是 document type 的缩写，在 web 中用来说明使用 HTML 的哪个版本。必须生命在第一行

`<!DOCTYPE html>` 就是声明使用 HTML 5

### `<Html></html>`

网页的根标签，所有的网页标签都在此标签内

### `<head></head>`

定义文档的头部，他是所有头部元素的容器，例如：title、script、style、link、meta 等标签

`<tile>网页标题</tile>` 标签之间的文字是网页的标题信息，你浏览器标签页上显示的就是这部分内容，title 用来告诉用户和搜索引擎这个网页的主要内容是什么

### `<body></body>`

是网页内容的标签，网页上显得内容都在此标签内，比如：h1、p、a、img 等

#### 段落标签 `<p>`

用来展示一个个的段落，默认样式：段前段后都会有空白：

```html
<p>我是一段文字</p>
<p>我是一段文字</p>
<p>我是一段文字</p>
```

<div style="border: 1px solid">
    <p>我是一段文字</p>
    <p>我是一段文字</p>
    <p>我是一段文字</p>
</div>

#### 强调标签 `<em>/<strong>`

在语调上，em 表示强调、strong 表示更强烈的强调。在浏览器中显示的方式也不同，em 用斜体展示，strong 用粗体展示

```html
<em>需要强调的文本</em>
<strong>需要强调的文本</strong>
```

<div style="border:1px solid">
    <em>需要强调的文本</em>
	<strong>需要强调的文本</strong>
</div>

#### `<span>标签`

span 标签是没有语义的，它的作用就是为了设置单独的样式，例如一段文字中想要特别展示某个单词。就可以使用 span 标签

```html
<p>
    我是与众不同的<span style="color:red">文字</span>
</p>
```

<div style="border:1px solid">
    <p>
        我是与众不同的<span style="color:red">文字</span>
    </p>
</div>

#### 引用标签 `<q>`

比如想要引用某句诗，那么就可以使用 q 标签

```html
<p>
    这句诗写的很好：<q>锄禾日当午</q>，你觉得怎么样
</p>
```

<div style="border:1px solid">
	<p>
        这句诗写的很好：<q>锄禾日当午</q>，你觉得怎么样
    </p>
</div>

可以发现被加上了双引号

#### 长文本引用 `blockquote`

这是对长文本的引用，比如引用某位专家的大段道理，就可以使用这个标签

```html
<p>
    我觉得这段话说的特别好：<blockquote>活着就是一切，活着就是乐，活着也有苦，苦里却也有乐；就如一片树叶，我该生的时候，我生气勃勃地来，长我的绿，现我的形，到该落的时候了，我痛痛快快地去，让别的叶子又从我的落疤里新生。</blockquote>你觉得呢?
</p>
```

<div style="border:1px solid">
	<p>
        我觉得这段话说的特别好：<blockquote>活着就是一切，活着就是乐，活着也有苦，苦里却也有乐；就如一片树叶，我该生的时候，我生气勃勃地来，长我的绿，现我的形，到该落的时候了，我痛痛快快地去，让别的叶子又从我的落疤里新生。</blockquote>你觉得呢?
    </p>
</div>

可以看到进行了缩进处理

#### 换行标签 `<br/>`

```html
<p>
    我在这一行，<br/>我想到下一行去
</p>
```

<div style="border: 1px solid">
	<p>
        我在这一行，<br/>我想到下一行去
    </p>
</div>

#### 水平线标签 `<hr/>`

hr 表示一条分割的横线，默认样式为灰色

```html
<p>
    你<hr/>我
</p>
```

<div style="border: 1px solid">
	<p>
        你<hr/>我
    </p>
</div>

#### 预格式化文本标签 `<pre>`

被包围在 pre 标签内的文本通常会保留空格和换行符

```html
<pre>我我    我我我我我我我我
我我我  我我我我我我我</pre>
```

<div style="border:1px solid">
    <pre>我我    我我我我我我我我
我我我  我我我我我我我</pre>
</div>

#### 无序列表 `<ul>`

```html
<ul>
    <li>a</li>
    <li>b</li>
    <li>c</li>
</ul>
```

<div style="border:1px solid">
    <ul>
        <li>a</li>
        <li>b</li>
        <li>c</li>
    </ul>
</div>

#### 有序列表 `ol`

```html
<ol>
    <li>a</li>
    <li>b</li>
    <li>c</li>
</ol>
```

<div style="border:1px solid">
    <ol>
        <li>a</li>
        <li>b</li>
        <li>c</li>
    </ol>
</div>

#### 容器标签 `<div>`

有一个概念叫：DIV + CSS 网页布局，通常 div 用来作为容器、用来排版等

比如要网页上展示一个地图，就可以使用 div，在给他一个 id，就能完美的体现它的作用了

```html
<style>
    #map{ width: 500px; height: 500px; }
</style>
<div id="map"></div>
```

#### 表格 `table`

一个 table 由 thead、tbody、tr、th、td 等标签组成

- table：整个表格以 `<table>` 开始，以 `</table>` 结束
- caption：为表格添加标题
- tbody：表示表格内容，不使用也可以，它的作用是：使用此标签后，表格的内容全部下载完毕才显示；不使用的话，边下载边显示
- tr：一行数据
- td：单元格，如果一行中有多个 td，则表示有多列
- th：表头单元格，默认样式粗体

```html
<table>
    <caption>名单</caption>
    <tbody>
    	<tr><th>姓名</th><th>性别</th></tr>
        <tr><td>zzl</td><td>m</td></tr>
        <tr><td>augustu</td><td>m</td></tr>
    </tbody>
</table>
```

<div style="border:1px solid">
    <table>
        <caption>名单</caption>
        <tbody>
            <tr><th>姓名</th><th>性别</th></tr>
            <tr><td>zzl</td><td>m</td></tr>
        	<tr><td>augustu</td><td>m</td></tr>
        </tbody>
    </table>
</div>

#### 链接标签 `<a>`

```html
<a href="http://www.imooc.com" title="鼠标悬浮会显示我" target="_blank">慕课</a>
```

- target="_blank"：在新标签页打开链接，默认当前标签页

<div style="border:1px solid">
	<a href="http://www.imooc.com" title="鼠标悬浮会显示我">慕课</a>
</div>

a 标签在电子邮件方面也有作用：

![]( http://img.mukewang.com/56cff737000150b714280550.jpg ) 

```html
<a href="mailto:1553543482@qq.com?subject=测试a标签&body=你好啊">发送邮件</a>
```

<div style="border:1px solid">
    <a href="mailto:1553543482@qq.com?subject=测试a标签&body=你好啊">发送邮件</a>
</div>

#### 图片标签 `<img>`

```html
<img src="不存在.png" alt="图片显示错误时显示的文本" title="鼠标在图片上悬浮时显示此文本">
```

- src：图片的链接
- alt：当图片显示不成功时显示的文本
- title：鼠标悬浮时显示的文本

<div style="border:1px solid">
    <img src="asd" alt="图片显示错误时显示的文本" title="鼠标在图片上悬浮时显示此文本">
</div>



















































































