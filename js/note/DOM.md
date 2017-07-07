## DOM

DOM(文档对象模型)是针对HTML和XML文档的一个API（应用程序编程接口）。DOM描绘了一个层次化的节点树，允许开发人员添加、移除和修改页面的某一部分。**IE中的所有DOM对象都是以COM对象的形式实现的，这意味着IE中的DOM对象与原生javascript对象的行为或活动特点并不一致。**

### 节点层次

DOM可以将任何HTML或XML文档描绘成一个由多层节点构成的结构。节点分为几种不同的类型，每种类型分别表示文档中不同的信息及标记。每个节点都拥有各自的特点、数据和方法。灵位也与其他节点存在某种关系。节点之间的关系构成了层次，而所有的页面标记则表现为一个特定节点为根节点的树形结构。如：
```
<!DOCTYPE html>
<html>
<head>
  <title></title>
</head>
<body>

</body>
</html>
```
文档节点只有一个子节点，即<html>元素，称之为文档元素。文档元素是文档的最外层元素，文档中的其他元素都包含在文档元素中。每个文档只能有一个文档元素。

#### Node 类型
DOM1级定义了一个Node接口，该接口将由DOM中的所有节点类型实现。这个Node接口在javascript中是作为Node类型实现的，除了IE之外，在其他所有浏览器中都可以访问到这个类型。javascript中的所有的节点类型都继承自Node类型。因此所有节点类型都共享着相同的基本属性和方法。

每个节点都有一个nodeType属性，用于表明节点类型。借点类型由在Node类型中定义的下列12个数值常量来表示，任何借点类型必居其一：
- Node.ELEMENT_NODE(1);
- Node.ATTRIBUTE_NODE(2);
- Node.TEXT_NODE(3);
- Node.CDATA_SECTION_NODE(4);
- Node.ENTITY_REFERENCE_NODE(5);
- Node.ENTITY_NODE(6);
- Node.PROCESSING_INSTRUCTION_NODE(7);
- Node.COMMENT_NODE(8);
- Node.DOCUMENT_NODE(9);
- Node.DOCUMENT_TYPE_NODE(10);
- Node.DOCUMENT_FRAGMENT_NODE(11);
- Node.NOTATION_NODE(12);

通过比较上面这些常量，可以很容易的确定节点的类型，如：
```
function(someNode.nodeType == Node.ELEMENT_NODE) { // 在IE中无效,因为IE中没有公开Node类型的构造函数
  alert('Node is an element.');
}
// 为了确保跨浏览器兼容，最好还是将nodeType属性与数字值进行比较
function(someNode.nodeType == 1) { // 在IE中无效,因为IE中没有公开Node类型的构造函数
  alert('Node is an element.');
}
```
1. nodeName 和 nodeValue属性
  要了解节点的具体信息，可以使用nodeName和nodeValue这两个属性。这两个属性的值完全取决于节点的类型。使用这两个值前，最好先检测一下节点的类型。
  ```
  function(someNode.nodeType == 1) { // 先检测节点类型，看他是不是一个元素
    value = someNode.nodeName; // nodeName 的值是元素的标签名， nodeValue的值则始终为null
  }
  ````
2. 节点关系
  每个节点都有一个childNodes属性，其中保存着一个NodeList对象。NodeList是一个类数组对象，用于保存一组有序的节点，可以通过位置来访问这些节点。**虽然通过方括号语法可以访问NodeList的值，而且这个对象也有length属性，但是他并不是一个Array的实例。**NodeList对象的独特之处在于，他实际上是基于DOM结构动态执行查询的结果，因此DOM结构的变化能够自动反映在NodeList对象中。

  ```
  var firstChild = someNode.childNodes[0];
  var firstChild = someNode.childNodes.item(1);
  var count = someNode.childNodes.length;

  // 可以使用Array.prototype.slice() 方法将类数组对象转化为数组, 需要注意的是在IE8及之前的版本中无效
  var arryOfNodes = Array.prototype.slice.call(someNode.childNodes, 0);  

  // 想要在IE中将ModeList转换为数组，必须手动枚举所有的成员
  function converToArray(nodes) {
    var array = null;
    try{
      array = Array.prototype.slice.call(nodes, 0);  
    } catch(ex) {
      array = new Array();
      for(var i = 0, len=nodes.length; i < len; i++) {
        array.push(nodes[i]);
      }
    }
    return array;
  }
  ```

  通过previousSibling和nextSibling属性可以访问同一列表中的其他节点。列表中的第一个的节点的previousSibling属性的值为null，列表中最后一个节点的nextSibling属性的值也为null。
  ```
  if(someNode.nextSibling === null) {
    alert("last node in the parent's childeNodes list.");
  } else if(someNode.previousSibling === null) {
    alert("first node in the parent's childeNodes list.");
  }
  ```
  someNode.firstChild 和 someNode.lastChild 的属性值分别指向其childNodes列表中的第一个和最后一个节点。

  hasChildNodes() 也是一个非常有用的方法。返回true表示该节点包含一个或多个子节点

3. 操作节点
  因为关系指针都是只读的，所以DOM提供了一些操作节点的方法

  appendChild(), 用于向childNodes列表的末尾添加一个节点。更新完成后返回新增的节点。**如果在调用appendChild()时传入的是父节点的第一个子节点，那么该节点就会变为父节点的最后一个子节点。**

  insertBefore()，用于将节点放在childNodes列表中的某个特定的位置上。接收两个参数： 要插入的节点和作为参照的节点。
  ```
  // 插入后变为最后一个子节点
  var returnedNode = someNode.insertBefore(newNode, null);
  alert(newMode == someNode.lastChild); // true
  // 插入后成为第一个子节点
  returnedNode = someNode.insertBefore(newNode, someNode.firstChild);
  alert(newMode == newNode); // true
  alert(newMode == someNode.firstChild); // true
  // 插入到最后一个子节点前面
  returnedNode = someNode.insertBefore(newNode, someNode.lastChild);
  alert(newMode == someNode.childNodes[someNode.childNodes.length-2]); // true
  ```

  replaceChild() 接收两个参数：要插入的节点和要替换的节点。要替换的节点将有这个方法返回并从文档中被移除，同时由要插入的节点占据其位置。

  removeChild() 移除节点

4. 其他方法
  cloneNode() 复制节点，接收一个布尔值参数，表示是否执行深复制。参数为true，执行深复制，复制节点及其整个子节点树，false时，执行浅复制，只复制节点本身。
  > cloneNode() 不会复制添加到DOM节点中的javascript属性。例如事件处理程序等。这个方法只复制特性、子节点，其他一切都不复制。IE中存在bug，即他会复制事件处理程序，所以在复制之前最好先移除时间处理程序

  normalize() 这个方法唯一的作用就是处理文档树中的文本节点。由于解析器的实现或DOM操作等原因，可能会出现文本借点不包含文本。或连续出现两个文本节点的情况。

#### Document类型
javascript通过Document类型表示文档。在浏览器中，document对象就是HTMLDocument（继承自Document类型）的一个实例，表示整个HTML页面。而且，document是window对象的一个属性。Document 节点的特性：
- nodeType 的值为 9
- nodeName 的值为 "#document"
- nodeValue 的值为 null
- parentNode 的值为 null
- ownerDocument 的值为 null
- 其子节点可能是一个DocumentType(最多一个)、Element(最多一个)、ProcessingInstruction或Comment。

Document类型可以表示HTML页面或者其他基于XML的文档。不过，最常见的应用还是作为HTMLDocument实例的document对象。通过这个额文档对象，不仅可以获取与月面相关的信息，而且还能操作页面的外观以及底层结构。

> 在Firefox、Safari、Chrome 和 Opera中，可以通过脚本访问Document类型的构造函数和原型，但在所有的浏览器中，都可以访问HTMLDocument类型的构造函数和原型，包括IE8及后续版本。

1. 文档的子节点
  可以通过documentElement属性（更快、更直接的访问该元素），该属性始终指向HTML页面中的<html>元素。也通过childNodes列表访问文档元素。

  document.body 在javascript代码中出现的拼非常高，所有的浏览器都支持document.documentElement 和 document.body 属性

  Document另一个可能的子节点是DocumentType。通常将<!DOCTYPE> 看成一个与文档其它部分不同的实体，可以通过doctype属性（在浏览器中是document.doctype）来访问他的信息。浏览器对document.doctype属性的支持差别很大.

2. 文档信息
  作为HTMLDocument的一个实例，document对象还有一些标准的Document对象所没有的属性。这些属性提供了document对象所表现的网页的一些信息
  - title属性，它包含这<title> 元素中的文本。通过这个属性可以取得当前页面的标题，也可以修改当前页面的标题，并反映在浏览器的标题中。
  - URL属性，它包含网页完整的URL，即地址栏中显示的URL
  - domain属性，它只包含页面的域名
  - referrer属性， 它保存着链接到当前页面的那个页面的URL。在没有来源页面的情况下，referrer属性中可能包含空字符串

  这些信息都存在于请求的HTTP头部，只不过通过这些属性让我们能够在javascript中访问它们而已。

3. 查找元素
  获取元素的错左可以使用document对象的几个方法来完成：
  - getElementById(), 接收一个参数：要获取元素的ID。区分大小写。
      ```
      <input type="text" name="myElement" value="text field" />
      <div id="myElement">a div</div>
      ```
      上面代码，在IE7中调用document.getElementById('myElement'), 返回input元素。其他所有浏览器中都返回div元素。为了避免IE中存在的这个问题，最好的办法就是不让表单字段的name特性和其他元素的ID相同.

  - getElementsByTagName(),接收一个参数: 要取得元素的标签名，返回的是包含零个或多个元素的NodeList。访问返回值的方法：
    ```
    var images = document.getElementsByTagName('img');
    alert(images.length); // 返回img元素的个数
    alert(images[0].src); // 返回第一个元素的src属性
    alert(images.item(0).src); // 返回第一个元素的src属性
    alert(images.namedItem('logo')); // 返回name属性为logo的元素
    ```

  - getElementsByName(), 这个方法只有HTMLDocument类型才有。返回带有给定name属性的所有元素。

4. 特殊集合
  除了属性和方法，document对象还有一些特殊的集合，这些集合都是HTMLDocument对象，为了访问文档常用的部分提供了快捷方式，包括：
  - document.anchors 包含文档中所有带name特性的<a>元素
  - document.applets 包含文档中所有带有<applet>元素，因为不再推荐使用<applet>元素，所以这个集合也不建议使用了
  - document.forms 包含文档中所有的<form>元素
  - document.images 包含文档中所有的<img>元素
  - document.links 包含文档中所有带href属性的<a>元素

5. DOM一致性检测
  document.implementation 属性用来检测浏览器实现了DOM的哪些部分。
  ```
  var hasXmlDom = document.implementation.hasFeature('XML', '1.0');
  ```

6. 文档写入
  将输出流写入到网页中的方法： write(), writeln(), open() 和 close()

#### Element 类型
Element类型用于表现XML或HTML元素。提供了对元素标签名、子节点及特性的访问。特征如下：
- nodeType 的值为 1
- nodeName 的值为元素的标签名
- nodeValue 的值为null
- parentNode 可能是Document 或 Element
- 其子节点可能是Element、Text、Comment、ProcessingInstruction、CDATASection或EntityReference

要访问元素的标签名，可以使用nodeName属性，或者使用tagName属性。在HTML中，标签名始终都已全部大写表示，而在XML（有事也包括XHTML）中，标签名则始终会与源码中的保持一致，如果不确定自己的脚本将会在HTML还是XML文件中执行，最好在比较之前将标签名转化为相同的大小写形式
```
if(element.tagName.toLowerCase() == "div") { 
  // todo ...
}
```

1. HTML 元素
  所有的HTMl元素都是有HTMLElement类型表示。不是直接通过这个类型，也是通过它的子类型来表示。HTMLElement类型直接继承自Element并添加了一些属性。如：
  - id 元素在文档中的唯一标识符
  - title 有关元素的附加说明信息，一般通过工具提示条显示出来
  - lang 元素内容的语言代码。很少使用
  - dir 语言的方向，值为“ltr（left-to-right，从左至右）” 或“rtl（right-to-left， 从右至左）” 也很少用
  - className 与元素的class特性单对应。即为元素指定的class类，没有将这个属性命名为class，是因为class是ECMAScript的保留字

  并不是所有的属性的修改都会在页面中直观的表现出来，如id或lang

2. 取得特性
  每个元素都有一个或多个特性，这些特性的用途是给出相应元素或其他美容的附加信息。操作特性的DOM方法有三个，分别是getAttribute(), setAttribute
  () 和 reomveAttribute()。这三个方法可以针对任何特性使用，包括哪些一HTMLElement类型属性的形式定义的特性。
  ```
  var div = document.getElementById("myDiv");
  alert(div.getAttribute('id')); // myDiv
  alert(div.getAttribute('class'));
  alert(div.getAttribute('title'));
  alert(div.getAttribute('lang'));
  alert(div.getAttribute('dir'));

  // 自定义属性
  <div id="myDiv" data-test="test"></div>   // HTML5规范，自定义特性应该加上data- 前缀

  var myAttr = div.getAttribute('data-test'); // test
  ```
  两个特殊属性：
  - style
  - onclick

3. 设置特性
  setAttribute() 接收两个参数：要设置的特性名和值。 如果属性已经存在替换原有属性，如果不纯在创建该属性并设置值。通过setAttribute()方法即可以操作HTML特性，也可以操作自定义特性，通过这个方法设置的特性名会被统一转化为小写形式。即ID转化为id

  removeAttribute() 删除元素特性,IE6 及更早版本不支持。

4. attributes 属性
  ELement类型是使用attributes属性的唯一一个DOM节点类型。attributes属性中包含一个NamedNodeMap。与NodeList类似。也是一个动态的集合。元素的每一个特性都有一个Attr节点表示。每个节点都保存在NamedNodeMap
  对象中。 NamedNodeMap对象拥有的方法：
  - getNamedItem(name) 返回nodeName属性等于name的节点
  - removeNamedItem(name) 从列表中移除nodeName属性等于name的节点
  - setName的Item(node) 向列表中添加节点，以节点的nodeName属性为索引
  - item(pos) 返回位于数字pos位置处的节点

  ```
  // 取得元素的id特性
  var id = element.attributes.getNamedItem('id').nodeValue;
  var id = element.attributes['id'].nodeValue;
  ```

5. 创建元素
  使用document.createElement() 方法可以创建新元素。接收一个参数： 要创建的元素的标签名。 在HTML文档中不区分大小写，而在XML(包含XHTML)文档中，则是区分大小写的。
  ```
  var div = document.createElement('div');
  div.id = 'newDiv';
  div.className = 'box';
  document.body.appendChild(div); // 把新创建的元素添加到文档的<body>元素中
  ```

6. 元素的子节点
  元素可以有任意数目的子节点和后代节点，因为元素可以是其他元素的子节点。元素的childNodes属性中包含了他的所有的子节点，这些子节点有可能是元素、文本节点、注释或处理指令。不同浏览器在看待这些节点方面存在显著的不同。
  ```
  <ui id="myList">
    <li>item 1</li>
    <li>item 2</li>
    <li>item 3</li>
  </ul>
  ```
  如果是IE来解析这些代码，那么ul 元素会有3个li子节点。但是如果是其他浏览器中，ul元素都会有7个元素，包括3个li元素和4个文本节点（表示li元素之间的空白符）,如果按下面写法讲元素间的空白符删除，那么所有浏览器返回相同数目的子节点。
  ```
  <ui id="myList"><li>item 1</li><li>item 2</li><li>item 3</li></ul>
  ```

#### Text 类型
文本节点由Text类型表示。它包含的是可以照字面解释的纯文本内容，纯文本可以使包含转义后的HTML字符，但不能包含HTML代码。特征如下：
- nodeType 的值为 3
- nodeName 的值为 "#text"
- nodeValue 的值为该节点所包含的文本
- parentNode 是一个Element
- 不支持子节点

可以通过nodeValue属性或data属性访问Text节点中包含的文本。这两个属性中包含的值相同。
操作节点中文本的方法：
- appendData(text) 将text添加到节点的末尾
- deleteData(offset, count) 从offset指定的未知开始删除count个字符
- insertData(offset, text) 在offset指定的位置插入text
- replaceData(offset, count, text) 用text替换从offset指定的位置开始到offset + count 为止处的文本
- splitText(offset) 从offset指定的位置将当前文本节点分成两个文本节点
- substringData(offset, count) 提取从offset指定的位置开始到offset+count位置处的字符串

1. 创建文本节点
  使用document.createTextNode() 接收一个参数： 要插入节点中的文本。
  ```
  var element = document.createElement('div');
  element.className = 'message';
  var textNode = document.createTextElement('hello world');
  element.appendChild(textNode);
  document.body.appendChild(element);
  ```
2. 规范化文本节点
  normalize() 将两个相邻文本节点合并
  ```
  var element = document.createElement('div');
  element.className = 'message';

  var textNode = document.createTextNode('hello world!');
  element.appendChild(textNode);

  var anotherTextNode = document.createTextNode('test!');
  element.appendChild(anotherTextNode);

  document.body.appendChild(element);

  alert(element.childNodes.length); // 2

  element.normalize();

  alert(element.childNodes.length); // 1

  alert(element.firstChild.nodeValue); // hellow world!test!
  ```

3. 分割文本节点
  splitText() 这个方法会将一个文本节点分成两个文本节点。
  ```
  var element = document.createElement('div');
  element.className = 'message';

  var textNode = document.createTextNode('hello world!');
  element.appendChild(textNode);

  document.body.appendChild(element);

  var newNode = element.firstChild.splitText(5);
  alert(element.firstChild.nodeValue); // hello
  alert(newNode.nodeValue); // world
  alert(element.childNodes.length); // 2
  ```

#### Comment 类型
注释在DOM中是通过Comment类型来表示的。特点：
- nodeType 的值为 8
- nodeName 的值为 "#comment"
- nodeValue 的值为注释的内容
- parentNode 可能是Document 或 Element
- 不支持子节点

Comment类型 和 Text类型 继承自相同的基类。因此他拥有除了splitText()之外的所有字符串操作方法。
```
<div id="myDiv"><!-- a comment --></div>

var div = document.getElementById('myDiv');
var comment = div.firstChild;
alert(comment.data); // a comment

var comment = document.createComment('a comment');
```

#### CDATASection 类型
只针对基于XML的文档，表示CDATA区域。继承自Text类型。特征：
- nodeType 的值为 4
- nodeName 的值为 "#cdata-section"
- nodeValue 的值为CDATA区域中的内容
- parentNode 可能是Document 或 Element
- 不支持子节点

#### DocumentType 类型
在web浏览器中并不常见，仅有Firefox、Safari 和 Opera支持它。 特征：
- nodeType 的值为 10
- nodeName 的值为 doctype的名称
- nodeValue 的值为 null
- parentNode 是Document
- 不支持子节点

#### DocumentFragment 类型
在所有的节点类型中，只有DocumentFragment在文档中没有对应的标记。特征：
- nodeType 的值为 11
- nodeName 的值为 "#document-fragment"
- nodeValue 的值为 null
- parentNode 的值为 null
- 子节点可以是Element, ProcessingInstruction、Comment、Text、CDATASection或EntityReference。

虽然不能把文档片段直接添加到文档中，但是可以将它作为一个仓库来使用，可以在里面保存将来可能会添加到文档中的节点。
```
var fragment = document.createDocumentFragment();
var ul = document.getElementById('myList');
var li = null;
for(var i = 0; i < 3; i++) {
  li = document.createElement('li');
  li.appendChild(document.createTextNode('item ' + i));
  fragment.append(li);
}
ul.appendChild(fragment);
```

#### Attr 类型
元素的特性在DOM中以Attr类型来表示。在所有的浏览器中（包括IE8），都可以访问Attr类型的构造函数和原型。从技术角度讲，特性就是存在于元素的attributes属性中的节点。 特性：
- nodeType 的值为 2
- nodeName 的值是特性的名称
- nodeValue 的值是特性的值
- parentNode null
- 在HTML中不支持子节点
- 在XML中子节点可以使Text或EntityReference

```
var attr = document.createAttribute('align');
attr.value = 'left';
element.setAttributes(attr);
alert(element.attributes['align'].value); // left
alert(element.getAttributeNode('align').value); // left
alert(element.getAttribute('align')); // left
```

### DOM 操作技术

#### 动态脚本
使用script元素可以向页面插入javascript代码，一种是通过src属性包含外部文件，另一种是用这个元素本身来包含代码。动态脚本，指的是在页面加载是不存在，但将来的某一时刻通过修改DOM动态添加的脚本。两种创建动态脚本的方式：一，插入外部文件 二，插入javascript代码。

#### 动态样式
能够把CSS样式包含到HTML页面中的元素有两个: <link> 用于包含来自外部的文件，<style>用于指定嵌入的样式。与动态脚本类似，所谓动态样式是指在页面刚加载时不存在的样式，动态样式是在页面加载完成后添加到页面中的。

使用DOM代码创建link
```
function loadStyles(url) {
  var link = document.createElement('link');
  link.rel = 'stylesheet';
  link.type = 'text/css';
  link.href = url;
  var head = document.getElementsByTageName('head')[0];
  head.appendChild(link); // 必须将link 添加到head中而不是body中
}
loadStyles('style.css');
```

#### 操作表格
table 元素是html 中最复杂的结构之一。要想创建表格，一般都必须设计表示表格行、单元格、表头等方面的标签。
为了方便创建表格，HTML DOM 还为<table> <tbody> <tr> 元素添加了很多属性和方法。

**Table 对象集合**

集合 | 描述
---------|----------
cells[] | 返回包含表格中所有单元格的一个数组。
rows[] | 返回包含表格中所有行的一个数组。
tBodies[] | 返回包含表格中所有 tbody 的一个数组。

**Table 对象属性**

属性 | 描述
---------|----------
align | 表在文档中的水平对齐方式。（已废弃）
bgColor | 表的背景颜色。（已废弃）
border | 设置或返回表格边框的宽度。
caption | 对表格的 <caption> 元素的引用。
cellPadding | 设置或返回单元格内容和单元格边框之间的空白量。
cellSpacing | 设置或返回在表格中的单元格之间的空白量。
frame | 设置或返回表格的外部边框。
id | 设置或返回表格的 id。
rules | 设置或返回表格的内部边框（行线）。
summary | 设置或返回对表格的描述（概述）。
tFoot | 返回表格的 TFoot 对象。如果不存在该元素，则为 null。
tHead | 返回表格的 THead 对象。如果不存在该元素，则为 null。
width | 设置或返回表格的宽度。

**标准属性**

属性 | 描述
---------|----------
className | 设置或返回元素的 class 属性。
dir | 设置或返回文本的方向。
lang | 设置或返回元素的语言代码。
title | 设置或返回元素的 title 属性。

**Table 对象方法**

方法 | 描述
---------|----------
createCaption() | 为表格创建一个 caption 元素。
createTFoot() | 在表格中创建一个空的 tFoot 元素。
createTHead() | 在表格中创建一个空的 tHead 元素。
deleteCaption() | 从表格删除 caption 元素以及其内容。
deleteRow() | 从表格删除一行。
deleteTFoot() | 从表格删除 tFoot 元素及其内容。
deleteTHead() | 从表格删除 tHead 元素及其内容。
insertRow() | 在表格中插入一个新行。

#### 使用 NodeList
理解 NodeList 以及 NamedNodeMap 和 HTMLCollection 是从整体上透彻理解DOM的关键所在。这三个集合都是动态的，换句话说，每当文档结构发生变化时。他们都会得到更新，因此，他们始终都会保存这最新、最准确的信息。从本质上说，所有的NodeList对象都是在访问DOM文档时实时运行的查询。例如下面代码会导致死循环：
```
var divs = document.getElementsByTagName('div');
var i, div;
for(i = 0; i < divs.length; i++) {
  div = document.createElement('div');
  document.body.appendChild(div);
}
```

### 总结
DOM 是语言中立的API，用于访问和操作HTML和CML文档。DOM1记讲HTML和CML文档信箱的看作一个层次化的节点数。可以使用javascript来操作这个节点树，从而改进底层文档的外观和结构。

DOM有各种节点构成，总结如下：
- 最基本的节点类型是Node，用于抽象的表示文档中一个独立的额部分。所有其他类型都继承自Node
- Document 类型表示整个文档，是一组分层节点的根节点。在javascript中，document对象是Document的一个实例对象。使用document对象，有很多种方式可以查询和获取节点
- Element节点表示文档中的所有HTML和XML元素，可以用来操作这些元素的内容和特性
- 另外还有一些节点类型，分别表示文本内容、注释、文档类型、CDATA区域 和 文档片段。

访问DOM的操作在很多情况下都很直观，不过在处理script和style元素是还是存在一些复杂性。由于这两个元素分别包含脚本和样式信息。因此浏览器通常会将它们与其他元素区别对待。

理解DOM的关键就是理解DOM对性能的影响。DOM操作往往是javascript程序中开销最大的部分，而因访问NodeList导致的问题最多。NodeList对象都是通天的，这就意味着每次访问NodeList对象。都会运行一次查询，因此最好的办法就是尽量减少DOM操作。