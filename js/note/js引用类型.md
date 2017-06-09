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
