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

##### CSS3 结构类

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
- **Element:nth-last-child()**：
- :nth-of-type()、:nth-last-of-type()、:first-of-type()、:last-of-type()、:only-child、:only-of-type、:empty

##### 否定选择器

##### 伪元素







































































