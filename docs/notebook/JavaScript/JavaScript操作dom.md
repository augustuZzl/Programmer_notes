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

前面创建的节点是一个孤立的节点，它并不存在 DOM 树中，还需要使用 appEndChild() 等方法添加到文档树中

#### appendChild

#### insertBefore

#### removeChild

#### replaceChild

























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

