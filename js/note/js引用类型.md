## js引用类型

> js引用类型的值（对象）是应用类型的一个实例。 引用类型是一种数据结构，用于将数据和功能组织在一起。

新对象是使用new 操作符后跟一个构造函数来创建的，构造函数本身就是一个函数，只不过该函数是出于创造新对象的目的而定义的。

如 var person = new Object();

这行代码创建了Object引用类型的一个新实例，然后把该实例保存在变量person中，使用的构造函数是Object。它只是为新对象定义了默认的属性和方法。

### Object 类型

创建Object实例的两种方式
- **使用 new 操作符后跟Object构造函数**
  ```
  var person = new Object();
  person.name = '张三';
  person.age = '22';
  ```
- **使用对象字面量方式，对象字面量是对象定义的一种简写形式**, 使用字面量创建不会调用Object的构造函数，除了firefox3以及更早的版本外
  ```
  var person = {
    name: '张三',
    age: '22'
  };
  ```

### Array 类型

创建一个数组有两种方式：
- 使用Array构造函数, 其中new操作符可以省略
  ``` 
  var colors = new Array(); 
  var colors = new Array(20);  // 20 会自动转化成数组长度
  var colors = new Array('red','blue','green');  
  ```
- 使用字面量创建, 使用字面量创建不会调用Array的构造函数，除了firefox3以及更早的版本外
  ```
  var color = ['red','blue','green'];
  ```
> 数组最多可包含4294967295 个元素，如果添加元素超过上限，就会发生异常。而创建一个厨师大小与这个上限值大小接近的数组，则可能会导致运行时间超长的脚本错误。

#### 检测数组

Array.isArray() // 支持的浏览器  IE9+, Firefox 4+, Safari 5+, Opera 10.5, Chrome ...

#### 转换方法

所有的对象都具有 toLocaleString(), toString() 和 valueOf() 方法。

['red','blue','green'].toString(); // red,blue,green

#### 栈方法 和队列方法

- 栈方法: push()将元素添加到数组末尾，返回数组长度， pop()移除数组末尾最后一项两个方法，返回删除项
- 队列方法：shift() 移除数组的第一项并返回删除项， unshift 添加任意一个元素并返回新数组的长度

> 栈是一种LIFO（last-in-first-out）后进先出的数据结构
> 队列数据结构的访问规则FIFO(first-in-first-out) 先进先出
```
let a = ['a', 'b', 'c'];
a.push('d'); // 末尾添加元素 'd', 返回4，数组长度
a.pop(); // 末尾移除的元素, 返回删除的元素 'd'
a.unshift('d');// 起始位添加元素'd' , 返回4， 数组长度
a.shift(); // 删除起始位元素，返回删除的元素a
```

#### 排序方法 sort() 和 reverse()

> sort() 方法按升序排列数组项。最小值在最前面，最大值在最后面，为了实现排序，sort() 会调用每个数组项的toString()进行转型，然后比较得到的字符串。已确定如何排序。即使数组的每一项都是好数字，sort() 比较的也是字符串。

正确排序，需要自定义比较方法，传入到sort中：

```
function compare(val1, val2) {
  if(val1 < val2) {
    return -1;
  } else if(val1 > val2) {
    return 1;
  } else {
    return 0;
  }
}
var value = [1, 15, 10, 5];
value.sort(compare); // 1， 5， 10， 15

```
> 如果只是想要将原来的数组倒序，使用reverse() 会更快一些。

#### 其他常用方法

- concat(), 连接多个数组，不会改变原来数组。 先创建一个当前数组的副本，然后将接受到的参数添加到这个副本末尾，返回新构建的副本数组，如过没有传递参数，则直接返回新构建的数组副本
- slice(), 从某个已有的数组返回选定的元素，不会改变原来数组。 如果slice() 的参数中有一个负数，则用数组长度加上该数来确定相应的位置。如果结束为止小于起始位置，则返回空数组。
- splice(), 删除元素，并向数组添加新元素。功能如下：
  - 删除：可以删除任意数量的项，如arr.splice(0, 2) 表示从第0位开始删除两项，返回删除的元素
  - 插入：可以插入任意数量的项，如arr.splice(2, 0, 'aa', 'bb') 表示从第2位开始，删除0项，添加 aa, bb，返回删除的元素
  - 替换：如arr.splice(2, 1, 'aa', 'bb') 表示从第2位开始，删除1项，添加 aa, bb，返回删除的元素
- indexOf(), 返回元素在数组中的位置
- 迭代方法：
  - every(), 对数组中的每一项运行给定函数，如果该函数对每一项都返回true，则返回true
  - filter(), 对数组中的每一项运行给定函数，返回该函数会返回ture的项组成的数组
  - forEach(), 对数组中的每一项运行给定函数，没有返回值
  - map(), 对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组
  - some(), 对数组中的每一项运行给定函数，如果该函数对任一项返回true，则返回true
- 归并方法,迭代数组的所有项，然后构建一个最终返回的值
  - reduce(), 从数组的第一项开始，逐个遍历到最后
  - reduceRight(), 从数组的最后一项开始，向前遍历到第一项

### Date 类型

创建一个日期对象： var now = new Date(); // 返回当前日期和时间

- Date.parse() 接收一个表示日期的字符串参数，然后尝试根据这个字符串返回相应的日期的好描述。接收的日期格式：
  - “月/日/年”，如 6/13/2017
  - “英文月 日,年”，如 January 13，2017
  - “英文星期几 英文月 日 年 时：分：秒 时区”

#### Date 对象方法

方法 |  描述
----------------|----------------------------------
Date()  | 返回当日的日期和时间。
getDate()  | 从 Date 对象返回一个月中的某一天 (1 ~ 31)。
getDay()  |  从 Date 对象返回一周中的某一天 (0 ~ 6)。
getMonth()  |  从 Date 对象返回月份 (0 ~ 11)。
getFullYear()  | 从 Date 对象以四位数字返回年份。
getYear()  | 请使用 getFullYear() 方法代替。
getHours()  |  返回 Date 对象的小时 (0 ~ 23)。
getMinutes()  |  返回 Date 对象的分钟 (0 ~ 59)。
getSeconds()  |  返回 Date 对象的秒数 (0 ~ 59)。
getMilliseconds()  | 返回 Date 对象的毫秒(0 ~ 999)。
getTime()  | 返回 1970 年 1 月 1 日至今的毫秒数。
getTimezoneOffset() |  返回本地时间与格林威治标准时间 (GMT) 的分钟差。
getUTCDate() |   根据世界时从 Date 对象返回月中的一天 (1 ~ 31)。
getUTCDay() |  根据世界时从 Date 对象返回周中的一天 (0 ~ 6)。
getUTCMonth() |  根据世界时从 Date 对象返回月份 (0 ~ 11)。
getUTCFullYear()  |  根据世界时从 Date 对象返回四位数的年份。
getUTCHours() |  根据世界时返回 Date 对象的小时 (0 ~ 23)。
getUTCMinutes() |  根据世界时返回 Date 对象的分钟 (0 ~ 59)。
getUTCSeconds() |  根据世界时返回 Date 对象的秒钟 (0 ~ 59)。
getUTCMilliseconds()  |  根据世界时返回 Date 对象的毫秒(0 ~ 999)。
parse()  | 返回1970年1月1日午夜到指定日期（字符串）的毫秒数。
setDate()  | 设置 Date 对象中月的某一天 (1 ~ 31)。
setMonth()  |  设置 Date 对象中月份 (0 ~ 11)。
setFullYear() |  设置 Date 对象中的年份（四位数字）。
setYear() |  请使用 setFullYear() 方法代替。
setHours()  |  设置 Date 对象中的小时 (0 ~ 23)。
setMinutes()  |  设置 Date 对象中的分钟 (0 ~ 59)。
setSeconds()  |  设置 Date 对象中的秒钟 (0 ~ 59)。
setMilliseconds() |  设置 Date 对象中的毫秒 (0 ~ 999)。
setTime() |  以毫秒设置 Date 对象。
setUTCDate()  |  根据世界时设置 Date 对象中月份的一天 (1 ~ 31)。
setUTCMonth() |  根据世界时设置 Date 对象中的月份 (0 ~ 11)。
setUTCFullYear()  |  根据世界时设置 Date 对象中的年份（四位数字）。
setUTCHours() |  根据世界时设置 Date 对象中的小时 (0 ~ 23)。
setUTCMinutes() |  根据世界时设置 Date 对象中的分钟 (0 ~ 59)。
setUTCSeconds()  | 根据世界时设置 Date 对象中的秒钟 (0 ~ 59)。
setUTCMilliseconds()  |  根据世界时设置 Date 对象中的毫秒 (0 ~ 999)。
toSource()  |  返回该对象的源代码。
toString()  |  把 Date 对象转换为字符串。
toTimeString()  |  把 Date 对象的时间部分转换为字符串。
toDateString()  |  把 Date 对象的日期部分转换为字符串。
toGMTString() |  请使用 toUTCString() 方法代替。
toUTCString() |  根据世界时，把 Date 对象转换为字符串。
toLocaleString() |   根据本地时间格式，把 Date 对象转换为字符串。
toLocaleTimeString() |   根据本地时间格式，把 Date 对象的时间部分转换为字符串。
toLocaleDateString() |   根据本地时间格式，把 Date 对象的日期部分转换为字符串。
UTC() |  根据世界时返回 1970 年 1 月 1 日 到指定日期的毫秒数。
valueOf() |  返回 Date 对象的原始值。

### RegExp 类型

表达式： var expression = /pattern/ flags;

flags:
  - g: 全局模式
  - i: 表示不区分大小写
  - m: 表示多行模式。

元字符都必须转义如：
  ( [ { \ ^ $ | ) ? * + . ] } 

### Function 类型

> ** 每个函数都是Function类型的实例 ，而且都与其他的引用类型一样具有属性和方法。由于函数是对象，因此函数名实际上也是一个指向函数对象的指针，不会与某个函数绑定。**

函数常用的函数声明语法：
```
function sum(num1, mum2) {
  return num1 + num2;
}

var sum = function(num1, num2) {
  return num1 + num2;
};

var sum = new Function("num1", "num2", "return num1 + num2");
```
**注意最后一种定义函数的方式是使用Function 构造函数。Function构造函数可以接收任意数量的参数，但是最后一个参数始终被看作是函数体。从技术角度讲，这是一个函数表达式，但是我们不推荐这种方法定义函数，因为这种语法会导致解析两次代码，第一次是解析常规的js代码，第二次是解析传入的构造函数中的字符串，从而影响性能。不过这种语法对于理解 函数是对象，函数名是指针 的概念非常只管。**

由于函数名仅仅是指向函数的指针，因此函数名与包含对象指针的其他变量没有什么不同，换句话说，一个函数可能会有多个名字。他都指向这个函数本身。

```
function sum(num1, mum2) {
  return num1 + num2;
}
var b = sum;
sum = null;
console.log(b); // sum定义了一个函数，然后有将函数赋值给b，虽然设置了sun=null，但是b的指针仍然指向函数，所以此处的b仍然是函数。
```


#### 没有重载
将函数名想象为指针，也有利于解释为什么ECMAScript中没有函数重载的概念

#### 函数声明与函数表达式
解析器在向执行环境中加载数据时，对函数声明和函数表达式并非一视同仁。**解析器会率先读取函数声明，并使其在执行任何代码之前可用（可以访问），以至于函数表达式，则必须等到解析器执行到它所在的代码行，才会真正被解析执行。这就是声明提升**

```
alert(sum(10, 10)); // 不会报 undefined，因为函数声明会被提升
function sum(val1, val2) {
  return val1 + val2;
}

alert(sum(10, 10)); // 会报 undefined
var sum = function(val1, val2) {
  return val1 + val2;
};

```

#### 作为值的函数
函数本身就是变量，所以函数也可以作为值来使用，也就是说，不仅可以像传递参数一样，把一个函数传递给另一个函数，也可以将一个函数作为另一个函数的返回值返回。

#### 函数内部属性
在函数内部，有两个特殊的对象：arguments 和 this。 

- arguments是一个类数组对象，包含这传入函数中的所有参数。 虽然arguments的主要用途是保存函数参数，但是这个对象还有一个名叫callee 的属性，该属性的指针，指向拥有这个arguments对象的函数。

  举例，经典的递归
  ```
  function factorial(num) {
    if(num <= 1) {
      return 1;
    } else {
      return num *  factorial(num -1);
    }
  }

  function factorial(num) {
    if(num <= 1) {
      return 1;
    } else {
      return num *  arguments.callee(num -1); // arguments.callee 始终指向拥有arguments对象的函数，所以函数名可以随便更改，不影响函数内的执行
    }
  }
  ```

- this 引用的是函数据以执行的环境对象，也可以说是this值（当在网页的全局作用域中调用函数时，this对象应用的就是window）

```
window.color = 'red';
var o = {color: 'blue'};
function sayColor() {
  return this.color;
}
sayColor(); // red
o.sayColor = sayColor;
o.sayColor(); // blue
```

#### 函数的属性和方法
函数是对象，因此函数也有属性和方法，

- 每个函数都包含两个属性: length 和 prototype
  - length 表示函数希望接收的命名参数的个数。
  - prototype 是保存引用类型所有实例方法的真正所在。
- 每个函数都包含两个非继承而来的方法： apply() 和 call()
  - apply() 接收两个参数，一个是在其中运行函数的作用域，另一个是参数数组(也可以是Array实例，也可以是arguments对象)
  - call() 第一个参数是this值，其余参数都可以直接传递给函数，也就是说，传递给函数的参数必须逐个列举出来

### 基本包装类型

ECMAScript 还提供了三种特殊引用类型： Boolean, Number 和 String。 **每当读取一个基本类型值的时候，后台就会创建一个对应的基本类型包装类的对象，从而让我们能够调用一些方法来操作这些基本类型**

```
var s1 = 'some text';
var s2 = s1.sumstring(2);
```

引用类型和基本类型的主要区别就是对象的生存周期，使用new 操作符创建的引用类型的实例，在执行流离开当前作用域之前都一直保存在内存中。而自动创建的基本包装类型的对象，则只存在于一行代码的执行瞬间，然后立即被销毁。这意味着我们不能在运行时为基本包装类型值添加属性和方法。
```
var s1 = 'some text';
s1.color = 'red';
console.log(s1.color); // undefined
```



