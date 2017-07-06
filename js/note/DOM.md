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
