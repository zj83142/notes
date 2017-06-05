## javascript 基础学习笔记(一)

javascript是一种专为网页交互而设计的脚本语言，一个完整的javascript实现包括三个部分：
- 核心（ECMAScript）, 有ECMA-262定义，提供核心语言功能
- 文档对象模型（DOM），提供访问和操作网页内容的方法和接口
- 浏览器对象模型（BOM），提供与浏览器交互的方法和接口

### ECMAScript

于浏览器没有依赖关系，我们常见的Web浏览器只是ECMAScript实现可能的宿主环境和自已。宿主环境不仅提供基本的ECMAScript实现，同时也会停工该语言的扩展，以便语言与环境之间对接交互。

大致说来，ECMAScript规定了javascript语言的以下几部分：
- 语法
- 类型
- 语句
- 关键字
- 保留字
- 操作符
- 对象

### 文档对象模型（DOM）

Document Object Model 的简称，是针对XML当经过扩展用于HTML的应用程序编程接口（API， Application Programming Interface）。 DOM把整个页面映射成一个多层节点结构。

> DOM并不是针对javascrpt的。很多别的语言也都实现了DOM。不过，在web浏览器中，基于ECMAScript实现的DOM已经成为javascript这门语言的一个重要组成部分。

### 浏览器对象模型（BOM）

Browser Object Model 的简称，开发人员使用BOM可以控制浏览器显示的页面以外的部分。HTML5之前，BOM没有相关的标准。HTML5致力于把很多BOM功能写入正式规范。从而使得很多关于BOM的困惑烟消云散。

从根本讲，BOM只处理浏览器窗口和框架，但是人们习惯上也把针对与浏览器的javascript扩展算作BOM的一方部分，例如：
- 弹出新浏览器窗口
- 移动、缩放和关闭浏览器窗口
- 提供浏览器详细信息的navigator对象
- 提供浏览器所加载页面的详细信息的location对象
- 提供用户显示分辨率详细信息的screen对象
- 对cookie的支持
- 想XMLHttpRequest 和 IE 的ActiveXObject 这样的自定义对象

## 在html中使用javascript

### <script> 元素
script有两种使用方法：直接在页面中嵌入javascript代码和包含外部javascript文件

script元素的六个属性
- async：可选。表示应该立即下载脚本，但是不应妨碍页面的其他操作，比如下载其他资源或者等待加载其他脚本，只对外部脚本文件有效
- charset：可选。表示通过指定src属性至指定的代码的字符集。大多浏览器会忽略，因此这个属性很少用
- defer： 可选。表示脚本可以延迟到文档完全被解析和显示之后在执行。只对外部脚本有效。IE7及更早版本对嵌入脚本也支持。
- src: 可选。表示包含要执行代码的外部文件。
- type：可选。可以看成是language（已废弃）的替代属性。表示编写代码使用的是脚本语言的内容类型（也称为MIME类型）

## 基本概念

- 区分大小写，ECMAScript中的一切（变量、函数名和操作符）都区分大小写。
- ECMAScript的变量是松散类型的。所谓松散类型就是可以用来保存任何类型的数据。使用var操作符定义的变量将成为定义改变两的作用域中的局部变量。这个变量在函数退出后就会被销毁。
- typeof 操作符， 用来检测给定变量的数据类型，**需要注意的是它是一个操作符，不是函数**

### 基本数据类型
- Undefined 表示为初始化，需要注意的是包含undefind值的变量和上位定义的变量还是不一样的
- Nulll 从逻辑角度来看，null值表示一个空指针对象。 **typeof 检测返回 object**
- Bollean 区分大小写，字面值只有：true和false。true、非空字符串、非零数字（包括无穷大）、任何对象都是true
- Number 表示整数和浮点数，NaN是一个特殊的数值（NaN与任何值都不相等，包括自身）
- String ECMAScript中的字符串是不可变的，要更改某个变量保存的字符串，首先要销毁原来的字符串。然后在用另一个包含新值的字符串赋值给改该变量
- Object 一组数据和功能的集合