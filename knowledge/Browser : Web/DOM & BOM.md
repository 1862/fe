# DOM&BOM

## DOM

Document Object Model: **文档对象模型**，也叫**文档树模型**，是一套用来操作`HTML`和`XML`的一套`API`

### Node类型

DOM将文档描绘成了一个多层次节点构成的结构，节点有很多的类型，每个类型又有不同的特点、数据和方法，节点之间也存在着相互的关系，同级关系，子父级关系等等。整个文档就是由一个个节点构建起来的树，就是我们经常说的DOM树。
每个节点（node）都有一个`nodeType`属性，表明节点的类型，下面先简单列举一下常见的节点类型：

- Node.ELEMENT_NODE(1)———–元素节点，nodeType值为1
- Node.ATTRIBUTE_NODE(2)———属性节点，nodeType值为2
- Node.TEXT_NODE(3)————–文本节点，nodeType值为3
- Node.COMMENT_NODE(8)———–注释节点，nodeType值为8
- Node.DOCUMENT_NODE(9)———-文档节点，nodeType值为9
- Node.DOCUMENT_TYPE_NODE(10)—-doctype节点，nodeType值为10

可以通过节点的`nodeType`值来判断节点类型

```
if(someNode.nodeType == 1){
  console.log("Node is an element.")
}
```

###节点关系

节点之间存在着这样或那样的关系，每个node都有一个`childNodes`属性，其中保存着一个`NodeList`对象，有`length`属性，但不是一个`Array`实例，可以像下面一样访问里面存着的node:

```
var fistChild = someNode.childNodes[0];
var secondChild = someNode.childNodes.item(1);
var count = someNode.childNodes.length;
```

还有一些属性：

- `someNode.nextSibling` 同级下一个节点
- `someNode.previousSibling` 同级前一个节点
- `someNode.firstChild` 节点的第一个子节点
- `someNode.lastChild` 节点的最后一个子节点
- `someNode.parentNode` 节点的父节点

###节点操作方法

####常见的操作方法

- `someNode.appendChild(newNode)`————–在节点的childNodes末尾添加一个新节点，返回new
- `someNode.insertBefore(newNode, whoNode)`—-在who前面添加一个new节点，返回new
- `someNode.replaceChild(newNode, whoNode)`—-将who替换为new节点，返回who
- `someNode.removeChild(whoNode)`————–将who移除，返回who

以上方法都是在父节点上的操作，没有子节点节点调用这些方法会报错。

####两个特例方法

这俩方法有没有子节点都可以使用：

- `cloneNode()`

  这个方法用来复制调用它的节点，可以传一个参数true来进行深复制；

  ```javascript
  //浅复制，只复制节点本身，不会复制节点内部
  var copyNode = someNode.cloneNode(false);
  
  //深复制，复制节点内部子节点
  var deepCopy = someNode.cloneNode(true);
  ```

要注意的一点是，`cloneNode`的默认参数在不同的规范下，取true和false都有可能，建议强制写明参数，不使用默认值（其实也不知道默认值到底是啥）。

- `normalize()`

  这个方法是文本节点(Text_Node)的专用方法，在someNode上调用时，如果在其后代节点找到了空文本节点，就删除这个空文本节点；如果找到了相邻的文本节点（一般来说直接写的html都只有一个文本节点，除非我们用createTextNode()这种方法创建文本节点，才有可能出现相邻的文本节点），则合并为一个节点。

###元素节点ELement_Node

元素节点简单来说就是html中的标签，什么`p`标签，`h1`标签等等，它的特性：

- `nodeType`值为1；
- `nodeName`值为元素标签名；也可以使用`tagName`获取，注意大小写问题
- `nodeValue`值为null；它也没啥值好用的
- `parentNode`可能为`Document`或`Element`；文档的子节点或者其他`element`的子节点
- 子节点可能是：`Element`、`Text`、`Comment`等等；

####HTML元素

所有的HTML元素都是由HTMLElement类型表示，它就是继承自Element，然后添加了一些属性，其实感觉跟ELement一个东西，主要有以下属性：

- id;————就是div的id么，标识符一个
- title;———就是那个title
- lang和dir;—–很少用
- className;—–class的名字结合

其中`className`属性，这个注意一下，返回的是一个class名称的字符串合集，比如”item active test”，需要自己用字符串方法拆分操作它，后来HTML5就新增了一个`classList`属性，返回的是个class数组，比[‘item’, ‘active’, ‘test’]，可以用数组的方式操作class名称，就很方便了。

`classList`属性有自己的几个操作方法：

- `add(value)`;———-将给定的字符串添加到列表，如果有，则不添加
- `contains(value)`;—–列表里面有没有给定的值，返回true/false
- `remove(value)`;——-删除列表中给定的值
- `toggle(value)`;——-没有就添加，有就删除

```javascript
div.classList.remove('item');
div.classList.add('current');
div.classList.toggle('active');
```

####操作特性

操作div元素的属性，就是常见的几种方法，列举一下：

- `getAttribute()`;
- `setAttribute()`;
- `removeAttribute()`;

我们还经常自定义属性，比如在标签里定义个`id='1'`，HTML5规范建议自定义属性加上`data-`前缀，表明是自定的属性，这样可以使用`div.dataset.id`取得属性值。

####创建元素节点

可以使用`document.createElement()`方法创建元素节点。再配合node的操作方法，进行各种穿插。

###属性节点Attribute_Node

属性节点不被认为是DOM文档树的一部分，简单说一下它的特性：

- `nodeType`值为2；
- `nodeName`值为特性的名称；
- `nodeValue`值为特性的值；
- `parentNode`为null
- 没有子节点

操作属性一般用上面的`getAttribute()`、`setAttribute()`、`removeAttribute()`。

###文本节点Text_Node

文本节点简单说就是标签里的内容，比如`<div>hello world</div>`中的’hello world’，先说一下它的特性：

- `nodeType`值为3；
- `nodeName`值为`#text`;
- `nodeValue`值为节点包含的文本；
- `parentNode`可能`Element`；
- 没有子节点；

开始和结束标签之间只要有内容，就会创建一个文本节点：

```html
//没有文本节点
<div></div>

//有空格，故有一个文本节点
<div> </div>

//有内容，故有一个文本节点
<div>hello world</div>
var textNode = div.firstChild;
textNode.nodeValue;  // hello world
```

####创建文本节点

可以通过`document.createTextNode('hello world')`创建一个文本节点，再配合node的操作方法，进行各种穿插。

```javascript
var element = document.createElement('div');
element.className = 'message';

var textNode = document.createTextNode('hello world');
element.appendChild(textNode);

var anotherTextNode = document.createTextNode('Yep!');
element.appendChild(anotherTextNode);   //这样就有了俩文本节点

alert(element.childNodes.length);   // 2
element.normalize();
alert(element.childNodes.length);      // 1
alert(element.firstChild.nodeValue);   // 'hello worldYep!'
```

#### 规范化文本节点

normalize() 将所有文本节点合并成一个节点

#### 分割文本节点

spiltText() 将一个文本节点分割成两个, 参数为位置

###文档节点Document_Node

文档节点表示整个HTML页面，`document`对象也是`window`对象的一个属性，说一下它的特性：

- `nodeType`值为9；
- `nodeName`值为`#document`;
- `nodeValue`值为null；
- `parentNode`值为null；
- 子节点可能是一个`DocumentType`、`Element`、`Comment`；

####子节点

document的子节点也就是常见的`html`、`head`、`body`、`doctype`等，可以用下面的属性取得它们的引用：

```javascript
var html = document.documentElement;
var head = document.head;            // HTML5新定义的
var body = document.body;
var doctype = document.doctype;
```

####文档信息

仅做了解：

```javascript
var originTitle = document.title;   // 取得页面的title
document.title = 'New Title';       // 设置页面title

var url = document.URL;             // 取得页面的完整URL
var domin = document.domin;         // 取得域名
var referrer = document.referrer;   // 取得来源页面的URL
```

####查找元素

- `document.getElementById()`

根据id获取元素的引用，如果找不到，就返回null，只能使用`document`调用。

- `getElementsByTagName()`

根据标签名获取一个`HTMLCollection`元素列表，跟`NodeList`对象类似。可以使用`document`或者`element`调用

```javascript
var images = document.getElementsByTagName('img');   // document调用
images.length;    // 数量
images[0].src;
images.item(0).src;

var test = element.getElementsByTagName('a');    // element调用
```



还可以传入一个`*`来获取文档中的所有元素。

- `getElementsByClassName()`

根据class类名查找，可以使用`document`或者`element`调用

```javascript
var test = document.getElementsByClassName("item active");
var test1 = element.getElementsByClassName("item");
```

####DOM的扩展

DOM还有一个`SelectorsAPI`的扩展，另一个是`HTML5`，下面说一下`SelectorsAPI`，都可以通过`element`或者`document`调用。

- `querySelector()`

接受一个CSS选择符，返回匹配到的第一个元素，没有找到就返回null。

```文档节点Document_Node文档节点表示整个HTML页面，document对象也是window对象的一个属性，说一下它的特性：nodeType值为9；nodeName值为#document;nodeValue值为null；parentNode值为null；子节点可能是一个DocumentType、Element、Comment；子节点document的子节点也就是常见的html、head、body、doctype等，可以用下面的属性取得它们的引用：var html = document.documentElement;var head = document.head;            // HTML5新定义的var body = document.body;var doctype = document.doctype;文档信息仅做了解：var originTitle = document.title;   // 取得页面的titledocument.title = &#39;New Title&#39;;       // 设置页面titlevar url = document.URL;             // 取得页面的完整URLvar domin = document.domin;         // 取得域名var referrer = document.referrer;   // 取得来源页面的URL查找元素document.getElementById()根据id获取元素的引用，如果找不到，就返回null，只能使用document调用。getElementsByTagName()根据标签名获取一个HTMLCollection元素列表，跟NodeList对象类似。可以使用document或者element调用var images = document.getElementsByTagName(&#39;img&#39;);   // document调用images.length;    // 数量images[0].src;images.item(0).src;var test = element.getElementsByTagName(&#39;a&#39;);    // element调用还可以传入一个*来获取文档中的所有元素。getElementsByClassName()根据class类名查找，可以使用document或者element调用var test = document.getElementsByClassName(&quot;item active&quot;);var test1 = element.getElementsByClassName(&quot;item&quot;);DOM的扩展DOM还有一个SelectorsAPI的扩展，另一个是HTML5，下面说一下SelectorsAPI，都可以通过element或者document调用。querySelector()接受一个CSS选择符，返回匹配到的第一个元素，没有找到就返回null。var body = document.querySelector(&#39;body&#39;);var myDiv = document.querySelector(&#39;#myDiv&#39;);querySelectorAll()同样接受一个CSS选择符，返回一个NodeList实例。var ems = document.getElementById(&#39;myDiv&#39;).querySelectorAll(&#39;em&#39;);var strongs = document.querySelectorAll(&#39;p strong&#39;);strongs.item(1);strongs[2];文档写入wirte();wirteIn();—–跟write()一样，只不过会在写入的字符串最后添加一个’/n’close();open();HTML5新增了innerHTML()和outerHTML()两个方法，不同的是outerHTML()会包含调用元素本身。另外一个需要了解的方法是contains()，用来判断某个节点是不是调用节点的子节点：document.documentElement.contains(document.body);   // true
var body = document.querySelector('body');
var myDiv = document.querySelector('#myDiv');
```

- `querySelectorAll()`

同样接受一个CSS选择符，返回一个`NodeList`实例。

```javascript
var ems = document.getElementById('myDiv').querySelectorAll('em');
var strongs = document.querySelectorAll('p strong');
strongs.item(1);
strongs[2];
```

####文档写入

- `wirte()`;
- `wirteIn()`;—–跟`write()`一样，只不过会在写入的字符串最后添加一个’/n’
- `close()`;
- `open()`;

HTML5新增了`innerHTML()`和`outerHTML()`两个方法，不同的是`outerHTML()`会包含调用元素本身。另外一个需要了解的方法是`contains()`，用来判断某个节点是不是调用节点的子节点：

```javascript
document.documentElement.contains(document.body);   // true
```

###其他

- 事件流

  - 默认冒泡 false
  - e.stopPropagation()
  - e.preventDefault()

- 事件代理

- 宽高和定位

  - clientWidth/clientHeight内容的容器内可见宽高

  - offsetWidth/offsetHeight内容的盒子(不包括margin)宽高

  - scrollWidth/scrollHeight内容真正的宽高

    > 参考JQuery

- 滚动条

  - e.target.scrollTop

- UI组件设计

  - 结构设计
  - API 设计
  - 控制流设计

### 标签内容

- `innerHTML`可以用于获取和设置标签的所有内容，包括`标签`和`文本内容`
- `innerText`可以用于获取和设置标签的文本内容，会丢弃掉标签

## BOM

- 常用API
  - alert / confirm

  - Location

    - **window.location** 只读属性, 返回一个 [`Location`](https://developer.mozilla.org/zh-CN/docs/Web/API/Location)  对象, 其中包含有关文档当前位置的信息 

  - navigator.userAgent
    - 识别浏览器的方法之一
    - 有弱规范, 很多厂商会隐藏或伪装自己的浏览器
    - http 请求时会发送, 服务器端也能够获得 (可用来统计)
    - 在某些特定情况下可以用来判断浏览器规避特定 bug
    - 在通常情况下不作识别浏览器差异化使用
      - 推荐采用特性检测

  - Cookie
    - 网站跟踪用户行为, 服务器用以标记 Session
    - 少量数据, 跟随 http 请求 (header) 发送
    - 可设置过期时间
    - 网站 Cookie 数量有限制
    - 支持 http-only

  - Storage
    - 存储的数据量比较大, 但仍有限制
    - 数据格式为字符串, 可以用 JSON 解析
    - 不会随着 http 请求发给服务器

  - XHR(Ajax)

  - WebSockets

  - Canvas

    - 用途

      - 绘制图形
      - 处理图片 (必须是同域)
      - 高性能动画
      - 2D 游戏

    - 为了获得这个接口的对象, 需要在 `<canvas>` 上调用 [`getContext()`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLCanvasElement/getContext) ,  并提供一个 "2d" 的参数 

      ```
      var ctx = canvas.getContext('2d');
      ```

    - [绘图API](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D)