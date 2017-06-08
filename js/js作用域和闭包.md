## javascript - 作用域和闭包

闭包（closure）是javascript语言的一个难点，也是它的一个特点，很多高级应用都要依靠闭包来实现，想要了解闭包，我们就需要先了解javascript特殊的作用域。

### 了解什么是作用域
简单来说作用域表示变量或函数能够被访问的范围，以及它们在什么样的上下文中被执行。javascript中函数内声明的所有变量在函数体内始终是可见的，在javascript中有全局作用域和局部作用域。局部变量的优先级高于全局变量。

**变量声明提前**

```
var scope="global";
function scopeTest(){
    console.log(scope);
    var scope="local"  
}
scopeTest(); //undefined
```
此处的输出是undefined，并没有报错，这是因为在前面我们提到的函数内的声明在函数体内始终可见，上面的函数等效于
```
var scope="global";
function scopeTest(){
    var scope;
    console.log(scope);
    scope="local"  
}
scopeTest(); //undefined
```
> 注意，如果忘记var，那么变量就被声明为全局变量了。在javascript中变量的作用范围是函数级的，即在函数中所有的变量在整个函数中都有定义，声明提前、全局变量优先级低于局部变量，根据这两条规则就不难理解为什么输出undefined了。

### 什么是闭包
作用域是理解闭包的一个前提，闭包是指在当前作用域内总是能访问外部作用域中的变量。简单点儿说就是，当内层函数应用一个外层函数中的变量时就形成了闭包。

```
function createClosure(){
    var name = "jack";
    return {
        setStr:function(){
            name = "rose";
        },
        getStr:function(){
            return name + ":hello";
        }
    }
}
var builder = new createClosure();
builder.setStr();
console.log(builder.getStr()); //rose:hello
```
