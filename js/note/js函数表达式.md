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
经典的递归阶乘函数。