## js小技巧积累

1. 空(null, undefined)验证
  ```
    // 一般写法
    if (variable1 !== null || variable1 !== undefined || variable1 !== '') { let variable2 = variable1; }

    // 简洁写法
    let variable2 = variable1  || '';
  ```

2. 数组
  ```
    // 一般写法
    let a = new Array(); 
    a[0] = "myString1"; 
    a[1] = "myString2"; 
    a[2] = "myString3";

    // 简洁写法
    let a = ["myString1", "myString2", "myString3"];
  ```

3. if true .. else 的优化
  ```
    // 一般写法
    let big;
    if (x > 10) {
        big = true;
    }
    else {
        big = false;
    }

    // 简洁写法
    let big = x > 10 ? true : false;
  ```

4. 变量声明
  ```
    // 一般写法
    let x;
    let y;
    let z = 3;

    // 简洁写法
    let x, y, z=3;
  ```

5. 赋值语句的简化
  ```
    // 一般写法
    x=x+1;
    minusCount = minusCount - 1;
    y=y*10;

    // 简洁写法
    x++;
    minusCount --;
    y*=10;
  ```

6. 避免使用RegExp对象
  ```
    // 一般写法
    var re = new RegExp("\d+(.)+\d+","igm"),
    result = re.exec("padding 01234 text text 56789 padding");
    console.log(result); //"01234 text text 56789"

    // 简洁写法
    var result = /d+(.)+d+/igm.exec("padding 01234 text text 56789 padding");
    console.log(result); //"01234 text text 56789"
  ```

7. If 条件优化
  ```
    // 一般写法
    if (likeJavaScript === true)

    // 简洁写法
    if (likeJavaScript)
  ```

8. 函数参数优化
  ```
    // 一般写法
    function myFunction( myString, myNumber, myObject, myArray, myBoolean ) {
      // do something...
    }
    myFunction( "String", 1, [], {}, true );

    // 简洁写法
    function myFunction() {
      /* 注释部分
      console.log( arguments.length ); // 返回 5
      for ( i = 0; i < arguments.length; i++ ) {
          console.log( typeof arguments[i] ); // 返回 string, number, object, object, boolean
      }
      */
    }
    myFunction( "String", 1, [], {}, true );
  ```

9. charAt()的替代品
  ```
    // 一般写法
    "myString".charAt(0);

    // 简洁写法
    "myString"[0]; // 返回 'm'
  ```

9. charAt()的替代品
  ```
    // 一般写法
    "myString".charAt(0);

    // 简洁写法
    "myString"[0]; // 返回 'm'
  ```