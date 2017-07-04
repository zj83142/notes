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