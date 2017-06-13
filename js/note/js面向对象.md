## js 面向对象

面向对象（Object-oriented，OO）的语言有一个标志，那就是他们都有类的概念。可以通过类创建任意多个具有相同属性和方法的对象。

ECMA-262 把对象定义为：无序属性的集合，其中属性可以包含基本值、对象或者函数。我们可以把ECMAScript的对象想象成散列表： 无非就是一组键值对。其中值可以是数据或函数。每个对象都是基于一个引用类型创建的，一个引用类型可以是原生类型，也可以是开发人员自定义的类型。

### 理解对象

```
// 第一种 创建一个Object实例，然后添加属性和方法
var person = new Object();
person.name = '张三';
person.age = 30;
person.job = "software engineer";
person.sayName = function() {
  return this.name;
}
// 第二种 对象字面量的写法，现在比较常见
var person = {
  name: '张三',
  age: 30,
  job: "software engineer",
  sayName: function() {
    return this.name;
  }
}

```

#### 属性类型

ECMAScript 中有两种属性： 数据属性和访问器属性

1. 数据属性
  数据属性包含一个数据值的未知。在这个位置可以读取和写入，有4个描述其行为的特性：
    - [[Configurable]]: 表示能否通过delete删除属性从而重新定义属性。能否修改属性的特性，或者能否把属性修改为访问器属性。直接在对象中定义的属性，特性默认true
    - [[Enumerable]]: 表示能否通过for-in循环返回属性，直接在对象中定义的属性，特性默认true
    - [[Writable]]: 表示能否修改属性的值，直接在对象中定义的属性，特性默认true
    - [[Value]]: 包含这个属性的数据值。读取属性值的时候，从这个位置读，写入属性值的时候把新值保存在这个未知，这个特性的默认值是undefined

  要修改属性默认的特性，必须使用ECMAScript 5 的Object.defineProperty() 方法。接收三个参数： 属性所在的对象，属性的名称和一个描述符对象。其中描述符对象的属性值必须是：configurable、enumerable、writable和value，可以设置其中的一或多个值。

  > IE8 是第一个实现Object.defineProperty() 的浏览器版本。然后这个版本的实现存在诸多限制：只能在DOM对象上使用这个方法。而且只能创建访问器属性。建议不要在IE8中使用。

2. 访问器属性
  访问器属性不包含数据值。它包含一对getter和setter函数，不过这两个函数都不是必须的。在读取访问器属性时，会调用getter函数，这个函数负责返回有效的值，在写入访问器属性时。会调用setter函数传入新值，这个函数负责决定如何处理数据。
  访问器属性的4个特性：
    - [[Configurable]]: 表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性。直接在对象中定义的属性，特性默认true
    - [[Enumerable]]: 表示能否通过for-in 返回属性，直接在对象中定义的属性，特性默认true
    - [[Get]]: 在读取属性时调用的函数，默认值为undefined
    - [[Set]]: 在写入属性时调用的函数，默认值为undefined

  访问属性值不能直接定义。必须使用Object.definedProperty()来定义。
  ```
  var book = {
    _year: 2016, // 下划线是一种常用标志。用于表示只能通过对象方法访问的属性
    edition: 1
  };
  Object.defineProperty(book, 'year', {
    get: function() {
      return this._year;
    },
    set: function(newValue) {
      if(newValue > 2016) {
        this._year = newValue;
        this.edition += newValue -2016;
      }
    }
  });
  book.year = 2017;
  alert(book.edition);
  ```

#### 定义多个属性
  Object.defineProperties() 可以通过描述符一次定义多个属性。这个方法接收两个对象参数：第一个对象是要添加和修改 其属性的对象。第二个参数与第一个对象中添加或修改的属性一一对应：
  ```
  var book = {};
  Object.definedProperties(book, {
    _year: {
      writable: true,
      value: 2016,
    },
    edition: {
      writable: true,
      value: 1
    },
    year: {
      get: function() {
        return this._year;
      },
      set: function(newValue) {
        if(newValue > 2016) {
          this._year = newValue;
          this.edition += newValue -2016;
        }
      }
    }
  });
  ```
#### 读取属性的特性

Object.getOwnPropertyDescriptor() 可以取得给定属性的描述符，接收两个参数： 属性所在的对象和要读取其描述符的属性名称，返回一个对象，如果是访问器属性，这个对象属性有configurable、enumerable、get和set。如果是数据属性，这个对象的属性有configurable、enumerable、writable和value

### 创建对象

