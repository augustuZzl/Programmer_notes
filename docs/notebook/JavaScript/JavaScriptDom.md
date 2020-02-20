JavaScript 包括 BOM( 浏览器对象模型 ) 和 DOM( 文档对象模型 )，而 DOM 就定义了访问和操作 HTML、XML 等文档的方法

**Node** 是 DOM 中所有节点都继承的接口，他定义了一些通用的属性和方法来操作 DOM 树

### nodeType

nodeType 是 Node 的一个 number 类型的属性，不同的值代表不同的节点类型

- Node.ELEMENT_NODE：1
- Node.ATTRIBUTE_NODE：2
- Node.TEXT_NODE：3
- Node.CDATA_SECTION_NODE：4
- Node.ENTITY_REFERENCE_NODE：5
- Node.ENTITY_NODE：6
- Node.PROCESSING_INSTRUCTION_NODE：7
- Node.COMMENT_NODE：8
- Node.DOCUMENT_NODE：9
- Node.DOCUMENT_TYPE_NODE：10
- Node.DOCUMENT_FRAGMENT_NODE：11
- Node.NOTATION_NODE：12 

```javascript
const div = document.querySelector("div")
console.log(div.nodeType)	// res: 1，表明 div 是一个元素节点
```

在众多类型中，常用的有 element、text、attribute、comment、document 等节点

#### Element

我们常用的 div、p、span 等**标签**就是元素节点

- nodeType 为 1
- nodeName 为元素标签名，tagName 也返回元素标签名
- nodeValue 为 null

#### Text

Text 表示文本节点，它包含的是纯文本内容，不能包含 html 代码

- nodeType 为 3
- nodeName 为 #text
- nodeValue 为文本内容
- 父节点是 element，没有子节点

#### Attr

Attr 类型表示元素的属性，相当于元素的 attribute 属性中的节点

- nodeType 为 2
- nodeName 是属性的名称
- nodeValue 是属性的值
- parentNode 为 null 

#### Document

Document 表示文档，在浏览器中，document 对象时 HTMLDocument 的一个实例，表示整个页面，他同时也是 window 对象的一个属性。

- nodeType 为 9
- nodeName 为 #document
- nodeValue 为 null
- parentNode 为 null
- 子节点可能是一个 DocumentType 或 Element

#### DocumentFragment

DocumentFragment 表示一种轻量级的文档，可能当作一个临时的仓库用来保存可能会添加到文档中的节点。

- nodeType 为 11
- nodeName 为 #document-fragment
- nodeValue 为 null
- parentNode 为 null

### 节点创建 API

#### creatElement

creatElement 通过传入一个标签名来创建一个元素：

`const div = document.creatElement("div")`

#### creatTextNode

creatTextNode 用来创建一个文本节点：

`const textNode = document.creatTextNode("这是一个文本节点")`

#### cloneNode

cloneNode 是用来返回调用方法的节点的一个副本，它接收一个 bool 参数，用来表示是否复制子元素

由于是对节点的复制，所以可能会产生许多问题：

- 如果被复制的元素有 id，那么由于 id 的唯一性，别忘了修改复制节点的 id
- 如果被复制的元素绑定了事件，那么会产生两种情况：
  - 通过 addEventListener 或 onclick 绑定的事件，副本节点不会绑定
  - 通过内联方式绑定的事件(`<div onclick="do()"><div>`)，副本节点会绑定

#### creatDocumentFragment

creatDocumentFragment 主要在添加大量节点时会用到。

每创建一个新元素，然后添加到 DOM 树中，这个过程会造成浏览器回流(即元素的大小和位置都会被重新计算)，所以大量的元素就会导致性能问题。

由于 DocumentFragment 不是 DOM 树的一部分，他是保存在内存中的，先将大量节点添加到 DocumentFragment 中，最后再将 DocumentFragment 添加到 DOM 树中

```javascript
document.querySelector("button").addEventListener("click", () => {
    const list = document.querySelector("list")
    const fragment = document.creatDocumentFragment()
    
    for(let i = 0; i < 100; i++){
        const li = document.creatElement("li")
        li.textContent = i
        fragment.appendChild(li)
    }
    
    list.appendChild(fragment)  
})
```

### 页面修改 API

前面创建的节点是一个孤立的节点，它并不存在 DOM 树中，还需要使用 appendChild() 等方法添加到文档树中

#### appendChild

将指定的节点添加到调用该方法的节点的末尾：parent.appendChild(child)

child 节点会作为 parent 节点的最后一个子节点

如果 child 是页面中已经存在的一个节点，那么执行后原先位置的节点会移除，相当于**移动**

#### insertBefore

将一个节点添加到一个参照节点的前面：parent.insertBefore(child, refNode)

- parent：要添加到的父节点
- child：要添加的节点
- refNode：参照节点，新节点会插到此节点之前，如果 refNode 传入 null 或 undefined，那么新节点插入到末尾

#### removeChild

顾名思义，就是移除某个子节点：parent.removeChild(child)，返回移除的节点

如果要删除的 child 不是 parent 的子节点，那么报错。确保移除：

```javascript
if(child.parentNode) {
    child.parentNode.removeChild(child)
}
```

#### replaceChild

使用一个节点替换一个节点：parent.replaceChild(newChild, oldChild)

#### 总结

- 不管是新增还是替换节点，如果节点原本存在于页面之上，那么原先位置节点移除
- 节点不管怎么移动，本身绑定的事件不会消失

### 节点查询型 API

#### document.getElementById

根据 id 返回 Element 类型的元素，否则返回 null

- id 大小写敏感
- 如果存在多个相同的 id，那么返回第一个元素

#### document.getElementByTagName

根据元素标签名获取元素，返回一个 HTMLCollection 类型

HTMLCollection 是一个自动变化的集合，一定要注意，看一个例子

```html
<body>
    <div></div>
    <div></div>
    <script>
        const divList = document.getElementsByTagName("div")
        console.log(divList.length)    // 2
        const newDiv = document.createElement("div")
        document.body.appendChild(newDiv)
        console.log(divList.length)    // 3
    </script>
</body>
```

在添加新的 div 之前，divList 长度为 2，添加了一个 div，并没有对 divList 做任何操作，但他还是感受到了变化，长度自动变为了 3。要注意以下：

-  如果要对 HTMLCollection 结合进行循环操作，最好将其长度进行缓存使用，避免循环计算
- 如果页面中不存在指定的标签，不会返回 null，而是返回一个空的 HTMLCollection
- "*" 代表所有标签

#### document.getElementByName

通过 name 属性来获取元素，返回一个即时的 NodeList 对象

- NodeList 也是随时变化的
- 在页面中，不是所有的元素都具有 name 属性，比如 div，但强行给 div 设置 name 属性，也可以以获取的到
- 在 IE 中，如果传入的 name 值与某个元素的 id 值一样，那么这个元素也会被获取到，所以 id 值和 name 值不要一样

#### document.getElementByClassName

根据元素的 class 返回一个 即时的 HTMLCollection，如果要获取多个 className，可以传入多个：

`const elements = document.getElementByClasName("class1 class2")`

#### document.querySelector 和 document.querySelectorAll

通过 css 选择器开查找元素

document.querySelector() 返回第一个匹配的元素(使用深度优先搜索来获取元素)，否则返回 null：

```html
<body>
    <div>
        <div class="test">第二级div</div>
    </div>
    <div class="test">
        第一级div
    </div>
    <script>
        const div = document.querySelector(".test")
        console.log(div.textContent)	// 第二级div
    </script>
</body>
```

这个例子有两个 div 的 class 都为 .test，第一个 div 虽然在第二级，但由于深度优先搜索，先被获取到

document.querySelectorAll()：获取所有匹配到的元素，并且可以匹配多个选择符：

`document.querySelectorAll("#test, .test")`

- 也是深度优先搜索，搜索元素顺序与选择器顺序无关
- 返回一个非即时的 NodeLIst，不会自动变化

### 节点关系型 API

在 html 文档中，每个节点之间的关系都是一个家谱，父子、兄弟等都是男的

#### 父关系

- parentNode：每个节点都有一个 parentNode
- parentElement：返回元素的父元素，与 parentNode 的区别是父节点不许是一个 Element，否则返回 null

#### 兄弟关系

- previousSibling：节点的前一个节点，没有则返回 null，有可能得到文本节点或注释节点
- previousElementSibling：返回前一个元素节点
- nextSibling：节点的后一个节点，没有则返回 null，有可能得到文本节点或注释节点
- nextElementSibling：返回最后一个元素节点

#### 子关系

- childNodes：返回一个**即时**的NodeList，可能会包括文本节点、注释节点等
- children：一个解释的 HTMLCollection，子节点都是 Element
- firstNode：第一个节点
- lastNode：最后一个子节点
- hasChildNodes()：判断是否包含子节点

### 元素属性型 API

#### setAttribute

根据名称和值修改元素的特性，如果不存在此属性，那么会添加此属性

语法格式：element.setAttribute(name, value)

```javascript
// 以下两句代码等价
element.setAttribute("id", "test")
element.id = "test"
```

#### getAttribute

返回特定的属性的值：element.getAttribute("id")

### 元素样式 API

#### window.getComputedStyle

获取应用到元素后的样式，假如某个元素并未设置高度而是通过其内容将其撑开，这是就可以通过 getComputedStyle 来获取

语法格式：const style = window.getComputedStyle(element, [pseudoEil])

pseudoElt 指定一个伪元素进行匹配。返回的 style 是一个 CSSStyleDeclaration 对象

#### getBoundingClientRect

返回元素的大小以及相对于浏览器可视窗口的位置

语法格式：const clientRect = element.getBoundingClientRect()

clientRect 是一个 DOMRect 对象，包括 x、y、width、height、 left、right、bottom，他是相对于可是窗口的距离，滚动位置发生改变时，他的值是会发生变化的。

### Node 部分源码

```javascript
// 返回节点所在文档的路径：file:///F:test/index.html
readonly baseURI: string;

// 返回孩子节点列表
// 注意：标签内的空白部分(比如缩进)会被视为文本，而文本又被视为文本节点
readonly childNodes: NodeListOf<ChildNode>;

// 返回第一个子节点
readonly firstChild: ChildNode | null;

// 返回最后一个子节点
readonly lastChild: ChildNode | null;

// 该节点是否存在 dom 树中。例如新创建的节点没有append,就返回false
readonly isConnected: boolean;

// 返回下一个兄弟节点
readonly nextSibling: ChildNode | null;

// 返回前一个兄弟节点
readonly previousSibling: Node | null;

// 返回节点的名字。例如 DIV
readonly nodeName: string;

// 返回节点的类型，用数字表示。详细见后面
readonly nodeType: number;

// 设置或返回节点的值
nodeValue: string | null;

// 返回节点所在文档：#document...
readonly ownerDocument: Document | null;

// parentElement与parentNode乍看返回值相同，但区别就在 Element 和 Node 上，Element 属于 Node，当父节点不是 Element 时，差别就出来了
// 返回父元素
readonly parentElement: HTMLElement | null;
// 返回父节点
readonly parentNode: Node & ParentNode | null;

// 设置或返回指定节点的文本内容，以及它的所有后代
textContent: string | null;

// 向节点的子节点列表末尾添加子节点
appendChild<T extends Node>(newChild: T): T;

// 赋值一个节点，如果参数为 true，那么节点的子代也被赋值
cloneNode(deep?: boolean): Node;
          
// 如果 other 是节点的子节点返回 true
contains(other: Node | null): boolean;

// 返回根节点：#document
getRootNode(options?: GetRootNodeOptions): Node;

// 是否含有子节点
hasChildNodes(): boolean;

// 在已有的子节点前插入新的子节点
insertBefore<T extends Node>(newChild: T, refChild: Node | null): T;

// 两个节点是否相等：两个节点类型相等、属性相等
isEqualNode(otherNode: Node | null): boolean;

// 两个节点是否相同：引用同一个对象
isSameNode(otherNode: Node | null): boolean;

// 移除某个子节点
removeChild<T extends Node>(oldChild: T): T;

// 替换某个子节点
replaceChild<T extends Node>(newChild: Node, oldChild: T): T;
```

