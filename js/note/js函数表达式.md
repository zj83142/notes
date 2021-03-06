## 函数表达式

函数表达式是javascript中的一个既强大又令人困惑的特性

**定义函数的两种方式：**
1. 函数声明
  ```
  function funName(arg0, arg1, arg2) {
    // 函数体
  }
  ```
  **函数声明提升：在执行代码之前就会读取函数声明**
  ```
  sayHi();    // 不会报错，因为在代码执行之前就会先读取函数声明
  function sayHi() {
    alert('Hi'); 
  }
  ```
2. 函数表达式
  ```
  var funName = function(arg0, arg1, arg2) {
    // 函数体
  };
  ```
  function后面没有标识符。这种情况下创建的函数叫做匿名函数(anonymous function)，匿名函数有时候也叫拉姆达函数。
  ```
  sayHi();        // 会报错
  var sayHi = function() {
    alert('Hi'); 
  }
  // 理解函数提升的关键，在于理解函数声明与函数表达式之间的区别
  // 一定不要这么做
  if(condition) {
    function sayHi() {
      alert('Hi');
    }
  } else {
    funcion sayHi() {
      alert('Ya,ha!');
    }
  }
  // 可以改为
  var sayHi;
  if(condition) {
    sayHi = function {
      alert('Hi');
    }
  } else {
    sayHi = funcion {
      alert('Ya,ha!');
    }
  }
  ```

### 递归
递归函数是在一个函数通过名字调用自身的情况下构成的
```
function factorial(num) {
  if(num <= 1) {
    return 1;
  } else {
    return num * factorial(num - 1);
  }
}
```
经典的递归阶乘函数，上面的写法表面上看起来没问题，但是下面的写法去可能到导致他出错；
```
var anotherFactorial = factorial;
factoral = null;
alert(anotherfactorial(4)); // 出错 调用的时候factorial已经不再是个函数了。
```
在这种情况下，使用arguments.callee 可以解决这个问题。
```
function factorial(num) {
  if(num <= 1) {
    return 1;
  } else {
    return num * arguments.callee(num - 1); //  arguments.callee 是一个执行正在执行的函数的指针，可以用他来实现对函数的递归调用
  }
}
```
在严格模式下，不能通过脚本访问arguments.callee， 访问这个属性会报错。可以使用函数表达式来达成相同的效果
```
var factorial = (function f(num) {
  if(num <= 1) {
    return 1;
  } else {
    return num * f(num - 1); // 这种写法在严格模式或非严格模式下都行得通。
  }
})
```

### 闭包
闭包是指有权访问另一个函数作用与中变量的函数。创建闭包的常见方式，在一个函数内部创建另一个函数。
```
function createComparisonFunction(propertyName) {
  return function(object1, object2) {
    var value1 = object1[propertyName]; // 之所以能访问到propertyName 是因为内部函数的作用域链中包含了外部函数的作用域。
    var value2 = object2[propertyName];
    if(value1 < value2) {
      return - 1; 
    } else if(value1 > value2) {
      return 1;
    } else {
      return 0;
    }
  };
}
```
**当某个函数被调用时，会创建一个执行环境（execution context）以及相应的作用域链。然后使用arguments和其他命名参数的值来初始化函数的活动对象（activation object）。但在作用域链中，外部函数的活动对象始终处于第二位，外部函数的外部函数处于第三位，直到作为作用域链重点的全局执行环境**

在函数执行过程中，为读取和写入变量的值，就需要在作用域链中查找变量。
```
function compare(value1, value2) {
  if(value1 < value2) {
    return -1;
  } else if(value1 > value2) {
    return 1;
  } else {
    return 0;
  }
}
var result = compare(5, 10);

```
![](../../imgs/js/js_function_1.jpg)

后台的每个执行环境都有一个表示变量的对象——变量对象。全局环境的变量对象始终存在，而像compare()函数这样的局部环境的变量对象，则只在函数执行的过程中存在。在创建compare()函数时，会创建一个预先包含全局变量对象的作用域链。这个作用域链被保存在内部的[[Scope]]属性中。当调用compare()函数时，会为函数创建一个执行环境，然后通过复制函数的[[Scope]]属性中的对象构建起执行环境的作用域链。此后，又有一个活动对象，被创建并被推入执行环境作用域链的前端。对于这个例子中的compare() 函数的执行环境而言，其作用域链中包含两个变量对象：本地活动对象和全局变量对象。显然，作用域链本质上是一个指向变量对象的指针列表，它只引用，但不实际包含变量对象。

无论什么时候在函数中访问一个变量时，就会从作用域链中搜索具有相对名字的变量，一般来讲，当函数执行完毕后，局部活动对象就会被销毁。内存中仅保存全局作用域，但是，闭包的情况又有所不同。

![](../../imgs/js/js_function_2.jpg)

> 由于闭包会携带包含它的函数的作用域，因此会比其他函数占用更多的内存。过度使用闭包可能会导致内存占用过多。因此只在绝对需要时在考虑使用闭包。

### 闭包与变量

作用域链的这种配置机制引出了一个值得关注的副作用，即闭包只能取得包含函数中任何变量的最后一个值。别忘了闭包所保存的是整个变量对象。而不是某个特殊的变量。
```
function createFunctions() {
  var result = new Array();
  for(var i = 0; i < 10; i++) {
    result[i] = function() {
      return i;
    };
  }
  return result;
}
```
因为每个函数的作用域链中都保存着createFunctions() 函数的活动对象，所以他们引用的都是同一个变量i。
```
function createFunctions() {
  var result = new Array();
  for(var i = 0; i < 10; i++) {
    result[i] = function(num) {
      return function() {
        return num;
      };
    }(i); 
  }
  return result;
}
```
定义了一个匿名函数，并将立即执行该匿名函数的结果赋值给数组。

### 关于this对象
在闭包中使用this对象也可能会导致一些问题。this对象是在运行时基于函数的执行环境绑定的：在全局函数中，this等于window，而当函数被作为某个对象的方法调用时，this等于那个对象，不过**匿名函数的执行环境具有全局性**， 因此其this通常指向window。但是有时候由于编写闭包的方式不同，这一点儿可能不会那么明显。
```
var name = "the window";
var object = {
  name: "my object",
  getNameFunc : function() {
    return function() {
      return this.name;
    };
  }
};
alert(object.getnameFunc()()); // the window (在非严格模式下)
```
以上代码先创建了一个全局变量name，又创建了一个包含name属性的对象，这个对象还包含一个方法——getNameFunc(), 它返回一个匿名函数，而匿名函数有返回this.name。由于getNameFunc()返回一个函数，因此在调用object.getNameFunc()() 就会立即调用他返回的函数，结果就是返回一个字符串，然而，这个例子返回的字符串是the window。 即全局name变量的值。

这是为什么呢？

每个函数在被调用时都会自动取得两个特殊变量： this 和 arguments。 内部函数在搜索这个变量时，智慧搜索到其活动对象为止，因此用于不可能直接访问外部函数中的这两个变量，不过，把外部作用域中的this对象保存在一个闭包能访问到的变量里，就可以让闭包访问该对象了。

```
var name = "the window";
var object = {
  name: 'my object',
  getNameFunc: function() {
    var that = this;
    return function() {
      return that.name;
    };
  }
};
alert(object.getNameFunc()()); // my object
```

### 模仿块级作用域
javascript没有块级作用域的概念，这意味着块语句中定义的变量，实际上是包含函数
```
function outputNumbers(count) {
  for(var i = 0; i < count; i++) {
    alert(i);
  }
  var i; // 重新声明变量, 这里的声明并不影响后面的i
  alert(i); // 计数 如果count = 10， 此处的i=10
}
```

javascript 不会告诉你是否多次声明了同一个变量。遇到重复声明，他们只会对后续的声明视而不见。匿名函数可以用来模仿块级作用域。

用作跨级作用域（通常称为私有作用域）的函数的语法：
```
(function() {
  // 这块
})();
```

** 以上代码定义了立即调用一个匿名函数。讲函数声明包含在一对圆括号中，表示它实际上是一个函数表达式，而紧随其后的另一对圆括号会立即调用这个函数。 **

> 变量只不过是值的另一种表现形式，因此可以把值直接传递给函数。

```
var someFunction = function() {
  //这里是块级作用域
};
someFunction();
```
先定义了一个函数，然后立即调用他，定义函数的方式是创建一个匿名函数，并把匿名函数赋值给变量comeFunction。而调用函数的方式实在函数名称后添加一对圆括号。

```
function(){
  //这里是块级作用域
}(); // 出错
```
这段代码会导致语法错误，是因为javascript将function关键字当作一个函数声明的开始，而函数声明后面不能跟圆括号。然而，函数表达式的后面可以跟圆括号。要**将函数声明转化成函数表达式**，如下：
```
(function(){
  //这里是块级作用域
})(); 
```
无论什么地方，只要临时需要一些变量，就可以使用私有作用域。
```
function outputNumbers(count) {
  (function() {
    for(var i = 0; i < count; i++) {
      alert(i);
    }
  })();
  
  alert(i); // 报错
}
```
上面代码，在for循环外部插入了一个私用作用域。在匿名函数中定义的任何变量，都会在执行结束时被销毁。而在私有作用域中能够访问变量count，是因为这个匿名函数是一个闭包，他能够访问作用域中的所有变量。

这种技术经常在全局作用域中被用在函数外部，从而限制向全局作用域中添加过多的变量和函数。一般来说，我们都应该尽量少向全局变量中添加变量和函数。在一个有很多开发人员功能参与的大型项目中，过多的全局变量和函数很容易导致命名冲突，通过创建私有作用域，每个开发人员既可以使用自己的变量，又不必担心搞乱全局作用域。如：
```
(function() {
  var now = new Date();
  if(now.getMonth() == 0 && now.getDate() == 1) {
    alert('happy new year');
  }
})();
```
这种做法可以减少闭包占用的内存问题，因为没有指向匿名函数的引用。只要函数执行完毕，就可以立即销毁器作用域链了。

### 私有变量

严格意义上讲，javascript中没有私有成员的概念，所有的对象属性都是公有的。不过，有私有变量的概念。任何函数中定义的变量，都可以认为是私有的，因为不能在函数外部访问这些变量。**私有变量包括函数的参数、局部变量和在函数内部定义的其他函数**

```
function add(num1, num2) {
  var sum = num1 + num2;
  return sum;
}
```

如果在函数内部创建一个闭包，那么闭包通过自己的作用域链可以访问函数内部的变量，利用这点儿，我们可以创建用于访问私有变量的共有方法。我们把有权访问私有变量和私有函数的方法成为 **特权方法(privileged method)**， 创建方法如下：

**在函数中定义特权方法。 **
  ```
  function MyObject() {
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction() {
      return false;
    }
    // 特权方法
    this.publicMethod = function() {
      privateVariable ++;
      return privateFunction();
    };
  }
  ```
  对这个例子而言，变量 privateVariable 和函数 privateFunction() 只能通过特权方法publicMethod() 来访问。在创建MyObject的失利后，除了使用publicMethod() 这个途径外，没有任何方法可以直接访问privateVariableprivateFunction()

  **利用私有和特权成员，可以隐藏哪些不应该被直接修改的数据**， 如：
  ```
  function Person(name) {
    this.getName = function() {
      return name;
    };
    this.setName = function(value) {
      name = value;
    };
  }
  var person = new Person('张三')；
  alert(person.getName()); // 张三
  person.setName('李四');
  alert(person.getName()); // 李四
  ```
  以上例子，getName() 和 setName() 都是特权方法，都可以构造函数外使用，而且都有权利访问私有变量name。但是在Person构造函数外部，没有任何方法访问name。由于这两个方法都是在构造函数内部定义的，他们作为比包能够通过作用域链访问name。私有变量name在Person的每个实例中都有不同，因为每次调用函数都会创建这两个方法。

> 构造函数模式的缺点是针对每个实例都会创建同样一组新方法，而是用静态私有变量来实现特权方法就可以避免这个问题

#### 静态私有变量

** 通过在私有作用域中定义变量或函数，同样可以创建特权方法。**

  ```
  (function() {
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction() {
      return false;
    }
    // 构造函数
    MyObject = function() {};  // 这个地方没有用var，是一个全局变量，可以在私有作用域外被访问到。在严格模式下未经生命的变量复制会导致错误。
    // 公有特权函数
    MyObject.prototype.publicMethod = function() {
      privateVariable ++;
      return privateFunction();
    };
  })();
  ```
  这个模式与在构造函数中定义特权方法的主要区别在于，私有变量和函数是有实例共享的。由于特权方法是在原型上定义的，因此所有实例都有这个函数，而这个特权方法，作为一个闭包，总是保存着对包含作用域的引用。
  ```
  (function() {
    var name = "";
    Person = function(value) {
      name = value;
    };
    Person.prototype.getName = function() {
      return name;
    };
    Person.prototype.setName = function(value) {
      name = value;
    }
  })();

  var person1 = new Person("张三");
  alert(person1.getName()); // 张三
  person1.setName('李四');
  alert(person1.getName()); // 李四
  var person2 = new Person("王五");
  alert(person1.getName()); // 王五
  alert(person2.getName()); // 王五
  ```
  变量name是一个静态的由所有实例共享的属性。也就是说，在一个实例上调用setName(), 就会影响所有的实例。以这种方式创建静态私有变量会因为是永远醒而增进代码复用，但是每个实例都没有自己的私有变量。所以到底使用实例变量还是静态私有变量，最终还是要根据需求而定。

> 多查找作用域链中的一个层次，就会在一定程度上影响查找速度。而这正是使用闭包和私有变量的一个明显不足之处。

#### 模块模式

模块模式是为单例创建私有变量和特权方法，所谓单例，指的就是只有一个实例的对象。按照惯例，javascript是以对象字面量方式来创建单例对象。
```
var singleton = {
  name: value,
  method: function() {
    // todo ...
  }
};

```
模块模式通过为单例添加私有变量和特权方法能够使其得到增强，其语法形式如下：
```
var singleton = function() {
  // 私有变量和私有函数
  var privateVariable = 10;
  function privateFunction() {
    return false;
  }
  // 公有特权函数
  return {
    publicProperty: true,
    publicMethod: function() {
      privateVariable ++;
      return privateFunction();
    }
  };
}();
```
将对象字面量作为函数的返回值。从本质上来讲，这个对象字面量定义的是单例的公共接口，这种模式需要对单例进行某些初始化，同时又在需要维护其私有变量时非常有用。如：
```
var application = function(){
  //私有变量和函数
  var components = new Array();

  // 初始化
  components.push(new BaseComponent());
  return {
    getComponentCount: function() {
      return components.length;
    },
    registerComponent: function(component) {
      if(typeof component == 'object') {
        components.push(component);
      }
    }
  };
}();
```
上面例子，创建了一个用于管理组件的application对象，在创建这个对象的过程中，先声明一个私有的components数组，并向数组中添加了一个BaseComponent的新实例(这里不必关系BaseComponent的代码，他只是用来展示初始化操作)。返回对象的两个方法。


#### 增强的模块模式

适用于哪些单例必须是某种类型的实例，同事还必须添加某些属性和方法对其加以增强的情况。如：
```
var singleton = function() {
  // 私有变量和私有函数
  var privateVariable = 10;
  function privateFunction() {
    return false;
  }
  // 创建对象
  var object = new CustomeType();

  object.publicMethod = function() {
    privateVariable ++;
    return privateFunction();
  };

  //返回这个对象
  return object;
}();
```
如果演示模块模式的代码是前面例子中的application对象必须是BaseComponent的实例，那么可以如下：
```
var application = function(){
  //私有变量和函数
  var components = new Array();

  // 初始化
  components.push(new BaseComponent());

  // 创建 application 的一个局部副本
  var app = new BaseComponent();
  // 公共接口
  app.getComponentCount = function() {
    return components.length;
  };
  app.registerComponent = function() {
    if(typeof component == 'object') {
      components.push(component);
    }
  };
  // 返回这个副本
  return app;
}();
```

### 总结

在javascript中，函数表达式市一中非常有用的技术。函数表达式可以无需对函数命名从而实现动态编程。匿名函数也称为拉姆达函数，是一种使用javascript函数的强大方式。

总结函数表达式的特点：

- 函数表达式不同于函数声明。函数声明要求有名字。没有名字的函数表达式也叫匿名函数
- 在无法确定如何引用函数的情况下，递归函数就会变得比较复杂
- 递归函数应该始终使用arguments.callee来递归调用自身，不要使用函数名。

在函数内部定义了其他函数时，就创建了闭包，闭包有权限访问内部的所有变量，原理如下：

- 在后台执行环境中，必报的作用域链包含着他自己的作用域，包含函数的作用域和全局作用域。
- 通常，函数的作用域及其所有变量都会在函数执行结束后被销毁。
- 但是，当函数返回了一个比包是，这个函数的作用域将会一直在内存中保存到闭包不存在未知。

使用闭包可以在javascript中模仿块级元素。

- 创建并立即调用一个函数，这样既可以执行其中的代码，又不会在内存中留下对该函数的应用
- 结果就是函数内部的所有变量都会被立即销毁 —— 除非讲某些变量废纸给了包含作用域中的变量。

闭包还可以用于在对象中创建私有变量。

- 及时javascript中没有正式的私有对象属性的概念，但是可以使用闭包来轻松实现共有方法，而通过共有方法可以访问在包含作用域中定义的变量

有方法可以访问在包含作用域中的变量。

- 有权访问私有变量的公有方法叫做特权方法
- 可以使用构造函数模式、原型模式来实现自定义的特权方法，也可以使用模块模式、增强的模块模式来实现单例的特权方法。

javascript中的函数表达式和闭包都是及其有用的特性。利用他们可以实现很多功能，不过，因为创建闭包必须维护额外的作用域，所以过度使用它们可能会占用大量内存。
