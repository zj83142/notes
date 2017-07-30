## JS 事件

javascript与HTML之间的交互是通过事件实现的。事件，就是文档或浏览器窗口中发生的一些特定的交互瞬间。可以使用侦听器（或处理程序）来预定事件，以便时间发生是至此那个相应的代码。这种在传统软件工程中被称之为观察员模式的模型，页面支持的行为（javascript代码）与页面的外观（html 和 CSS 代码）之间的松散耦合。

DOM2 级规范开始尝试以一种复合逻辑的方式来标准化DOM事假。IE9、Firefox、Opera、Safari 和 Chrome全部已经实现了‘DOM2 级事件’模块的核心部分。IE8 是最后一个任然使用其他专有事件系统的主要浏览器。

### 事件流

事件流描述的是从页面中接收到事件的顺序。IE的事件流是事件冒泡流，而Netscape Communicator 的事件流是事件捕获流。

#### 事件冒泡

IE的事件流叫做事件冒泡（event bubbing），即事件开始时有最具体的元素（文档中嵌套村官次最深的那个节点）接收，然后逐级向上传播到较为不具体的节点（文档）。如：

```
<!DOCTYPE html>
<html>
<head>
  <title>Event Bubbing Example</title>
</head>
<body>
  <div id="myDiv">click me</div>
</body>
</html>
```
如果你单击了页面中的div元素，那么这个click事件会按照如下顺序传播：
  div ——> body ——> html ——> document

所有现代浏览器都支持事件冒泡，但是在具体实现上还是有一些差别。IE5.5 及更早的版本中的事件冒泡会跳过 html 元素（从body直接跳到document）。IE9、Firefox、Chrome和Safari则将事件一直冒泡到window对象。

#### 事件捕获

事件捕获的思想是不太具体的节点应该更早的收到事件，而最具体的节点应该最后收到事件。事件捕获的用以在于在事件到达预定目标之前捕获它。以前面简单的html页面为例，点击div元素后，按照如下顺序传播：
  document ——> html ——> body ——> div

虽然事件捕获是Netscape Communicator 唯一支持的事件流模型，但是IE9、Safari、Opera 和 Firefox 目前也都支持这种事件流模型。尽管“DOM2 级事件”规范要求事件应该从document对象开始传播，但这些浏览器都是从window对象开始捕获事件的。

#### DOM 事件流

“DOM2 级事件”规定的事件流包括三个阶段：时间捕获阶段、处于目标阶段和事件冒泡阶段。实现发生的是事件捕获，为截获事件提供机会。然后是实际上的目标接收到事件。最后一个阶段是冒泡阶段。可以在这个阶段对事件做出相应。以前面简单的html页面为例，点击div元素后的触发事件顺序：

![](../../imgs/js/js-event1.png)

在DOM事件流中，实际的目标（div元素）在捕获阶段不会接收到事件。这意味着在捕获阶段，事件从document到html再到body后就停止了，下一个阶段是“处于目标”阶段，于是事件在div上发生，并在事件处理中被看成冒泡阶段的一部分，然后，冒泡阶段发生，事件又传播到文档。

### 事件处理程序

事件就是用户或浏览器自身执行的某种动作。诸如click、load和mouseover，都是事件的名称，而响应某个事件的函数就叫做函数处理程序（或事件侦听器）。事件处理程序的名称以 on 开头，因此click事件的处理程序就是onclick，load事件的事件处理程序就是onload。

#### HTML事件处理程序

某个元素支持的某种时间，都可以使用一个与相应事件处理程序同名的HTML特性来指定。这个特性的值应该是能够执行的javascript代码，如：
```
<input type="button" value="click me" onclick="alert('clicked')"> />
```
通过指定onclick特性，将一些javascript代码作为它的值来定义。由于这个值是javascript，因此不能在其中使用未经转移的html语法字符，如&、""、小于号 <、大于号 >。

在HTML中定义的事件处理程序可以包括要至此那个的具体动作。也可以调用在页面其他地方定义的脚本，如：
```
<script type="text/javascript">
  function showMessage() {
    alert('hello world!');
  }
</script>
<input type="button" value="click me" onclick="showMessage();" />
```
这样指定事件处理程序具有一些独到之处，首先，这样会创建一个封装这元素属性的函数，这个函数中有一个局部变量event，也就是事件对象。
```
<!-- 输出 click -->
<input type="button" value="click me" onclick="alert(event.type);" />
```
通过event变量，可以直接访问事件对象，你不用自己定义，也不用从函数的参数列表中读取，在这个函数的内部，this值等于事件的目标元素，如：
```
<!-- 输出 click me -->
<input type="button" value="click me" onclick="alert(this.value);" />
```
关于这个动态创建的函数，另一个有意思的地方是它扩展作用于的方式。在这个函数内部，可以向访问局部变量一样访问document及该元素本身的成员。这个函数使用with扩展作用域：
```
function() {
  with(document) {
    with(this) {
      // 元素属性值
    }
  }
}
```
这样，事件处理程序要访问自己的属性就简单多了，如：
```
<!-- 输出 click me -->
<input type="button" value="click me" onclick="alert(value);" />
```
如果当前元素是一个表单输入元素，则作用域中还会包含访问表单元素（父元素）的入口，如：
```
function() {
  with(document) {
    with(this.form) {
      with(this) {
        // 元素属性值
      }
    }
  }
}
```
实际上，这样扩展作用域的方式，无非就是想让事件处理程序无需引用表单元素就能访问其他表单字段。如：
```
<form method="post">
  <input type="text" name="username" value="" />
  <input type="button" value="Echo Username" onclick="alert(username.value)" />
</form>
```
这样扩展事件处理程序的作用域链在不同浏览器中会导致不同的结果。不同javascript引擎遵循的标识符解析规则略有差异，很可能会在访问非限定对象成员时出错。

通过HTML指定事件处理程序的最后一个缺点是HTML与javascript代码紧密耦合，如果要更改事件处理程序，就要改动两个地方：html代码和javascript代码。

#### DOM0 级事件处理程序
通过javascript指定事件处理程序的传统方式，就是将一个函数赋值给一个事件处理程序属性，这种为事件处理程序赋值的方法是在第四代web浏览器中出现的，而且至今仍然为所有现代浏览器所支持。原因一是简单，二是具有跨浏览器的优势，要使用javascript指定事件处理程序，首先必须取得一个重要的对象的引用。

每个元素（包括window 和document）都有自己的事件处理程序属性，这些属性通常全部消协，例如onclick。将这种属性的值设置为一个函数，就可以指定事件处理程序。如：
```
var btn = document.getElementById('myBtn');
btn.onclick = function() {
  alert('Clicked');
}
```
使用DOM0 级方法指定的事件处理程序被认为是元素的方法。因此，有时候的事件处理程序在元素的作用域中运行，换句话说，程序中的this应用当前元素。如：
```
var btn = document.getElementById('myBtn');
btn.onclick = function() {
  alert(this.id); // myBtn
}
```
不仅仅是id，实际上可以在事件处理程序中通过this访问元素的任何方法和属性。以这种方式添加的事件处理程序会在事件流的冒泡阶段被处理。

也可以删除通过DOM0 级方法指定的事件处理程序，如：
```
btn.onclick = null; // 删除事件处理程序
```

#### DOM2 级事件处理程序

"DOM2 级事件"定义了两个方法：addEventListener() 和 removeEventListener() 用于处理指定和删除事件处理程序。所有的节点都包含这两个方法，并且他们都接受3个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值。布尔值如果是true，表示在捕获阶段调用事件处理程序，如果是false表示在冒泡阶段调用事件处理程序。

要在按钮上为click事件添加事件处理程序，可以使用下面代码：
```
var btn = document.getElementById('myBtn');
btn.addEventListener('click', function() {
  alert(this.id);
}, false);  // 该事件会在魔炮阶段被触发
```
使用DOM2 级方法田间事件处理程序的主要好处是可以添加多个事件处理程序。如：
```
var btn = document.getElementById('myBtn');
btn.addEventListener('click', function() {
  alert(this.id);
}, false);
btn.addEventListener('click', function() {
  alert('haha!!!');
}, false);
```
上面为按钮添加了两个事件处理程序，这两个事件处理程序会按照添加他们的顺序触发，因此首先显示元素ID，其次会显示haha!!!消息。

通过addEventListener() 添加的事件处理程序只能用removeEventListener()来移除；移除时传入的参数与添加处理程序时使用的参数相同，这意味着通过addEventListener() 添加的匿名函数讲无法移除，如：
```
var btn = document.getElementById('myBtn');
btn.addEventListener('click', function() {
  alert(this.id);
}, false);

btn.removeEventListener('click', function() { // 无效
  alert(this.id);
}, false);
```
上面例子中，我们在使用addEventListener() 添加了一个事件处理程序。虽然调用removeEventListener() 时看似使用了相同的参数，但实际上，第二个参数与传入addEventListener()中的那个是完全不同的函数。确保传入的参数相同，如：
```
var btn = document.getElementById('myBtn');
var hander = function() {
  alert(this.id);
}
btn.addEventListener('click', hander, false);

btn.removeEventListener('click', hander, false); // 有效
```

大多数情况下，都是将事件处理程序添加到事件流的冒泡阶段，这样可以最大限度的兼容各种浏览器，最好只在需要在事件到达目标之前捕获它的时候将事件处理程序添加到捕获阶段，如果不是特别需要，不建议在事件捕获阶段注册时间处理程序。

#### IE事件处理程序

IE 实现了与DOM中类似的两个方法：attachEvent() 和 detachEvent() 这两个方法接收相同的两个参数：事件处理程序名称与事件处理程序函数。由于IE8及更早的版本值支持事件冒泡，所以通过attachEvent() 添加的事件处理程序都会被添加到冒泡阶段。

使用attachEvent() 为按钮添加一个事件处理程序，如下：
```
var btn = document.getElementById('myBtn');
btn.attchEvent('onclick', function() {
  alert('Clicked!!!');
});
```
在IE中使用attachEvent() 与使用DOM0 级方法的主要区别在于事件处理程序的作用域。**在使用DOM0级方法的情况下，事件处理程序会在其所属元素的作用域内运行；在使用attachEvent() 方法的情况下，事件处理程序会在全局作用域中运行，因此this等于window。在编写跨浏览器的代码时，需要牢记这一区别**
```
var btn = document.getElementById('myBtn');
btn.attachEvent('onclick', function() {
  alert(this == window); // true
});
```

使用attachEvent() 添加的事件可以通过detachEvent() 来移除，条件是必须提供相同的参数，与DOM方法一样，这也意味着添加的匿名函数将不能被移除，不过只要能够将对象相同函数的引用传递给detachEvent(),就可以移除相应的事件处理程序，如：
```
var btn = document.getElementById('myBtn');
var handler = function() {
  alert('Clicked!');
};
btn.attachEvent('onclick', handler);
// 这里省略了其他代码
btn.detachEvent('onclick', handler);
```
这个例子将保存在变量handler中的函数作为事件处理程序，因此后面的detachEvent() 可以使用相同的函数来移除事件处理程序。

> 支持IE事件处理程序的浏览器有IE 和 Opera

#### 跨浏览器的事件处理程序

恰当的使用能力检测，保证处理事件的代码能在大多数浏览器下一致的运行，基本上只需关注冒泡阶段。

第一个要创建的方法是addHandler(), 他的职责是视情况分别使用DOM0级方法、DOM2级方法或IE方法来添加事件，这个方法属于一个叫EventUtil的对象。addHandler接收三个参数：要操作的元素、事件名称 和 事件处理程序函数。与addHandler() 对应的方法是removeHandler() 用来移除之前添加的事件处理程序 —— 无论该事件是采用什么方式添加到元素中的。
```
var EventUtil = {
  addHandler: function(element, type, handler) {
    if(element.addEventListener) {
      element.addEventListener(type, hander, false);
    } else if(element.attachEvent) {
      element.attachEvent('on' + type, handler);
    } else {
      element['on' + type] = handler;
    }
  },
  removeHandler: function(element, type, handler) {
    if(element.removeEventListener) {
      element.removeEventListener(type, handler, false);
    } else if(element.detachEvent) {
      element.detachEvent('on' + type, handler);
    } else {
      element['on' + type] = null;
    }
  }
};
```
这两个方法首先都会检测传入的元素是否窜在DOM2级方法，如果存在DOM2 级方法，则使用该方法：传入事件类型、事件处理程序函数和第三个参数false（表示）

### 事件对象

在触发DOM上的某个事件时，会产生一个事件对象event，这个对象中包含着所有与事件有关的信息。包括导致事件的元素、事件的类型以及其他与特定事件相关的信息。例如，鼠标操作导致的事件对象中，会包含鼠标位置的信息，而键盘操作导致的事件对象中，会包含与按下的键有关的信息。所有浏览器都支持event对象，但支持的方式不同。

#### DOM 中的事件对象

兼容DOM的浏览器会将一个event对象传入到事件处理程序中。无论制定事件处理程序时使用什么方法（DOM0级或DOM2级），都会传入event对象。
```
var btn = document.getElementById('myBtn');
btn.onclick = function(event) {
  alert(event.type); // click
};
btn.addEventListener('click', function(event) {
  alert(event.type); // click
}, false);
```
这个例子中的两个事件处理程序都会弹出一个警告框，显示由event.type属性表示的事件类型。这个属性始终都会包含被触发的事件类型。例如'click'(与传入 addEventListener() 和 removeEventListener() 中的事件类型一致)

在通过HTML特性制定事件处理程序时，变量event中保存着event对象。如：
```
<input type="button" value="Click Me" onclick="alert(event.type)" />
```


#### IE 中的事件对象

#### 跨浏览器的事件对象


### 事件类型

#### UI 事件

#### 焦点事件

#### 鼠标与滚轮事件

#### 键盘与文本事件

#### 复合事件

#### 变动事件

#### HTML5 事件

#### 设备事件

#### 触摸与手势事件

### 内存和性能

#### 事件委托

#### 移除事件处理程序


### 模拟事件

#### DOM 中的事件模拟

#### IE 中的事件模拟

### 小结