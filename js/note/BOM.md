## BOM

- 理解window对象 —— BOM的核心
- 控制窗口、框架和弹出窗口
- 利用location 对象中的页面信息
- 使用navigation 对象了解浏览器

> ECMAScript 是javascript的核心，但是如果要在web中使用javascript，那么BOM（浏览器对象模型）则无疑才是真正的核心。BOM提供了很多对象，用于访问浏览器的功能，这些功能与任何网页内容无关。多年以来，缺少实际上的规范，导致BOM既有意思又有问题，因为浏览器提供商会按照各自的想法随意去扩展它。于是浏览器之间总有的新对象就成为了实际上的标准。这些对象在浏览器中的已存在，很大程度上是由于他们提供了与浏览器的互操作性。W3C为了把浏览器中javascript最基本的部分标准化，已经将BOM的主要方面纳入了HTML5的规范中。

### window对象

BOM的核心对象是window，它表示浏览器的一个实例。在浏览器中，window对象有双重角色，它既是通过javascript访问浏览器窗口的一个接口，又是ECMAScript规定中的Global对象。这意味着在网页定义的任何一个对象、变量和函数，都以window作为其Global对象，因此有访问parseInt() 等方法。

#### 全局作用域

由于window对象同时有扮演者ECMAScript中Global对象的角色。因此所有在全局作用域中声明的变量、函数都会变成window对象的属性和方法。

抛开全局变量会成为window对象的属性不谈。定义全局变量与在window对象上直接定义属性还是有区别的：**全局变量不能通过delete操作符删除，而直接在window对象上定义的属性可以**
```
var age = 29;
window.color = 'red';
// 在IE < 9 时抛出错误。在其它所有浏览器中都返回false
delete window.age;
// 在IE < 9 时抛出错误，在其它所有浏览器中都返回true
delete window.color; // return true

alert(window.age); //  29
alert(window.color); // undefined
```

#### 窗口关系及框架

如果页面中包含框架，则每个框架都拥有自己的window对象，并且保存在frames集合中，可以通过数值索引（从0开始，从左到右，从上到下）或者框架名称来访问相应的window对象。每个window对象都有一个name属性，其中包含框架的名称。
```
<html>
	<head>
		<title>Frameset Example</title>
	</head>
	<frameset rows="160, *">
		<frame src="frame.html" name="topFrame" />
		<frameset cols="50%, 50%">
			<frame src="anotherframe.html" name="leftFrame" />
			<frame src="yetanotherframe.html" name="rightFrame" />
		</frameset>
	</frameset>
</html>
```
在使用框架的情况下，浏览器中会存在多个Global对象。在每个框架中定义的全局变量会自动成为框架中window对象的属性。由于每个window对象都包含原生类型的构造函数，因此每个框架都有一套自己的构造函数，这些构造函数意义对应，但并不相等。

#### 窗口位置

用来确定和修改window对象位置的属性和方法有很多。IE、Safari、Opera和Chrome都提供了screenLeft和screenTop属性，分别表示窗口相对于屏幕左边和上边的位置。Firefox则在screenX和screenY属性中提供了窗口的位置信息。Safari和Chrome也同时支持这两个属性。Opera虽然也支持，但是与screenLeft和screenTop属性并不对应，因此建议大家不要在Opera中使用它们

```
// 跨浏览器获取窗口左边和上边的位置
var leftPos = (typeof window.screenLeft == 'number') ? window.screenLeft : window.screenX;
var topPos = (typeof window.screenTop == 'number') ? window.screenTop : windwo.screenY;
```

#### 窗口大小

跨浏览器确定一个窗口的大小不是意见简单的事。IE9+、Firefox、Safari、Opera和Chome均为此提供了4个属性：innerWidth、innerHeight、outerWidth和outerHeight。

```
// 虽然无法最终确定浏览器窗口本身的大小，但却可以获取页面市口的大小
var pageWidth = window.innerWidth,
	pageHeight = window.innerHeight;
if(typeof pageWidth != 'number') {
	if(document.compatMode == 'CSS1Compat') { // 判断是否处于标准模式
		pageWidth = document.documentElement.clientWidth;
		pageHeight = document.documentElement.clientHeight;
	} else {
		pageWidth = document.body.clientWidth;
		pageHeight = document.body.clientHeight;
	}
}
```

另外使用resizeTo() 和 resizeBy()方法可以调整浏览器窗口的大小。这两个方法都接收两个参数，其中resizeTo()接收浏览器窗口的新宽度和新高度。而resizeBy()接收新窗口原窗口宽度和高度只差。如：
```
// 调整到100 * 100
window.resizeTo(100, 100);
// 调整到 200 * 150
window.resizeBy(100, 50);
// 调整到 300 * 300
window.resizeTo(300, 300);
```
> 需要注意的是，这两个方法与移动窗口位置的方法类似。也有可能被浏览器禁用；而且在Opera和IE7（及更高版本）中默认就是禁用的。另外这两个方法同样不适用于框架，而只能对外层的window对象使用

#### 导航和打开窗口

使用window.open() 方法既可以导航到一个特定的URL。也可以打开一个新的浏览器窗口，4个参数：要加载的URL、窗口目标，一个特性字符串以及一个表示新页面是否取代浏览器历史记录中的当前加载页面的布尔值。通常只需传递第一个参数。最后一个参数只在不打开新窗口的情况下使用。

