#JavaScprit中Var与Function对象
1. Javascript 是网景(Netscape)公司开发的一种基于客户端浏览器、~~基于~~**面向对象**、事件驱动式的网页脚本语言。前身叫做LiveScript。
2. JavaScrpit声明变量使用Var与不使用Var的区别：定义在函数外的变量一定是全局变量；定义在函数内的变量，如果声明了 var，那么该变量就是局部变量，<font color=red>**如果不声明var，那么该变量就是全局变量**</font>。
```html
<!DOCTYPE html>
<html>
  <head>
    <title>声明变量使用Var与不使用Var的区别</title>

    <script type="text/javascript">
        var globe = "javascprit";
        test();
        function test(){
            var local = "vbscript";
            //local = "vbscript";    //如果用这种方式在函数里声明变量则是全局变量
            document.writeln(globe);
            document.writeln(local);
        }
        document.writeln(globe);
        document.writeln(local);
    </script>
  </head>
  <body>
  </body>
</html>
```
3. **在JavaScript中函数（function）就是对象**。
```html
<!DOCTYPE html>
<html>
  <head>
  <script type="text/javascript">
      function add(number)
      {
          window.alert(number += 20);
      }
      // 在js中函数就是对象，所以上面的写法可以转为为以下写法
      var add = function(number)
      {
          window.alert(number += 20);
      }

      // 转换下面的
      function add(number, number2)
      {
          window.alert(number += 30);
      }
      // 将引用add重新指向了新的对象function(number, number2)
      var add = function(number, number2)
      {
          window.alert(number += 30);
      }

      add(10);    // js中给函数传参不要求每个参数都必须要写
      //执行那个函数要看这引用最后指向的是那个对象，这里跟java相同。如果上面函数顺序颠倒则引用指向赋值也发生变化。
  </script>
  </head>
  <body>
  </body>
</html>
```
4. 在JavaScript中没有，没有方法（函数）重载的概念。
上面的例子通过自定义函数的方式实现如下：
```html
<!DOCTYPE html>
<html>
  <head>
  <script type="text/javascript">

    var add = new Function("number", "window.alert(number + 20);");

      var add = new Function("number", "number2", "window.alert(number + number2);");

      add(10, 30);
  </script>
  </head>
  <body>
  </body>
</html>
```
5. **在JavaScript中有一个Function对象，所有自定义的函数都是Function 对象类型的。Function对象接收的所有参数都是字符串类型的，其中最后一个参数就是要执行的函数体，而前面的参数是函数真正需要接受的参数。**
6. 在JavaScript中，每个函数都有一个隐含的对象arguments，表示给函数**实际**传递的参数。