## DOM 扩展

对DOM的连个主要的扩展是： Selectors API 和 HTML5

### 选择符 API
Selectors API (www.w3.org/TR/selectors-api) 是由W3C发起指定的一个标准，致力于让浏览器原生支持CSS查询。所有实现这一功能的javascript库都会写入一个基础的CSS解析器，然后在使用已有的DOM查询方法查询文档并找到匹配的节点。

Selectors API Level 1 的核心是两个方法： querySelector() 和 querySelectorAll()。目前已经完全支持的浏览器有IE8+、Firefox 3.5+、Safari3.1+、Chrome 和 Opera10+

#### querySelector() 方法
querySelector() 接收一个CSS选择符，返回与该模式匹配的第一个元素。如果没有找到匹配元素，返回null。

#### querySelectorAll() 方法
querySelectorAll() 接收一个CSS选择符，返回的是一个NodeList实例。具体来说，返回的值其实删是带有所有属性和方法的NodeList。而其底层实现则类似于一组元素的快照，而非不断对文档进行搜索的动态查询。这样实现可以避免使用NodeList对象而引起的大多数性能问题。

### matchesSelector() 方法
Selectors API Level 2 规范为Element类型新增了一个方法matchesSelector()，接收一个CSS选择符，如果调用元素与该选择赴匹配，返回true，否则返回false。
```
if(document.body.matchesSelector('body.page1')) {
  // todo ...
}
```
貌似浏览器还不支持此方法

### 元素遍历
对于元素间的空格，IE9及之前的版本不会返回文本节点。而其他所有浏览器都会返回文本节点。这样就导致了再使用childNodes和firstChild等属性时的行为不一致，为了弥补这一差异，而同时又保持DOM规范不变。Element Traversal 规范(www.w3.org/TR/ElementTraversal/)新定义了一组属性。
- childElementCount 返回子元素的个数（不包括文本节点和注释）
- firstElementChild 指向第一个子元素
- lastElementChild 指向最后一个子元素
- previousElementSibling 指向前一个同辈元素

支持 Element Traversal 规范的浏览器有 IE9+、Firefox3.5+、Safari4+、Chrome 和 Opera10+

### HTML5
HTML5 规范围绕这如何使用新增标记定义了大量的javascript API

#### 与类相关的扩充
- getElementsByClassName() 方法是最受人欢迎的一个方法，可以通过document对象及其所有HTML元素调用该方法。这个方法最早出现在javascript库中，是通过既有的DOM功能实现的，而原生的实现具有及其大的性能优势。

  getElementsByClassName() 接收一个参数： 一个包含一个或多个类名的字符串。返回带有指定类的所有元素的NodeList。传入多个类名时，类名的先后顺序不重要。

  支持的浏览器有IE 9+、Firefox 3+、Safari 3.1+、Chrome 和 Opera 9.5+

- classList 属性
  在操作类名时，需要通过className属性添加、删除和替换类名。为了让操作更方便也更安全，可以为所有的元素添加classList属性，这个属性是心机和类型DOMTokenList的实例。有length属性，和取得每一个元素的方法item(),也可以用[]语法。 另外还有其他方法：
  - add(value) 将给定的字符串值添加到列表中
  - contains(value) 表示是否存在给定值
  - remove(value) 删除给定字符串
  - toggle(value) 如果已经存在，删除它。如果不存在，添加它。

  支持的浏览器有Firefox 3.6 + 和 Chrome

#### 焦点管理
HTML5 也添加了辅助管理DOM焦点的功能。首先就是document.activeElement属性。这个属性始终会应用DOM中当前获取了焦点的元素。元素活的焦点的方式有页面加载、用户输入（通过按tab键）和在代码中调用focus()

document.hasFocus() 用于确定文档是否获取了焦点

通过检测文档是否获得了焦点，可以知道用户是不是正在与页面交互。

支持的浏览器有IE 4+、Firefox 3+、Safari 4+、Chrome 和 Opera 8+

#### HTMLDocument的变化
1. readyState 属性 可能有两个值：loading 正在夹在文档、complete 已经加载完文档
  ```
  if(document.readyState == 'complete') {
    // todo ...
  }
  ```
  支持的浏览器有IE 4+、Firefox 3.6+、Safari、Chrome 和 Opera 9+

2. 兼容模式
  ```
  if(document.compatMode == 'CSS1Compat') {
    alert('standards mode');
  } else {
    alert('quirks mode');
  }
  ```
  支持的浏览器有Firefox、Safari 3.1+、Chrome 和 Opera

3. head 属性 document.head

  ```
  var head = document.head || document.getElementsByTagName('head')[0];

  ```
  支持的浏览器 Chrome 和 Safari 5

#### 字符集属性
HTMl5 新增了几个与文档字符集有关的属性：charset 和 defaultCharset

支持charset 的浏览器有IE、Safari、Opera 和 Chrome

支持defaultCharset 的浏览器有IE、Safari 和 Chrome

#### 自定义数据属性
HTML5 规定可以为元素添加非标准的属性，但要添加前缀 data- 目的是为元素提供与渲染无关的信息。或者提供语音信息。
```
<div id="myDiv" data-appId="12345" data-myname="test"></div>
```
可以通过dataset属性来访问和设置自定义属性的值，如 myDiv.dataset.appId 或 myDiv.dataset.appId = 6666

支持的浏览器有：Firefox 6+ 和 Chrome

#### 插入标记
给文档插入大量新HTML标记

1. innerHTML 属性
不支持 innerHTML属性的元素有：<col>、<colgroup>、<frameset>、<head>、<html>、<style>、<table>、<tbody>、<thead>、<tfoot>、<tr>

2. outerHTML 属性

3. insertAdjacentHTML() 

4. 内存与性能问题

#### scrollIntoView() 方法
可以在所有的HTML元素上调用，通过滚动浏览器窗口或某个容器元素，调用元素就可以出现在视口中，如果给这个方法传入true作为参数。或者传入任何参数，那么窗口滚动之后会让调用元素的顶部与视口顶部尽可能平齐。如果传入false作为参数，调用元素会尽可能全部出现在视口中。不过顶部不一定平齐。实际上，为某个元素设置焦点也会导致浏览器滚动并显示出获取焦点的浏览器。

支持的浏览器有 IE、Firefox、 Safari 和 Opera

### 专有扩展

#### 文档模式

#### children 属性

#### contains() 方法

判断一个节点是不是另一个节点的后代
```
alert(document.documentElement.contains(document.body)); // true
```

#### 插入文本

1. innerText 属性

2. outerText 属性

#### 滚动

1. scrollIntoViewIfNeeded(allignCenter)

2. scrollByLines(lineCount)

3. scrollByPages(pageCount)
