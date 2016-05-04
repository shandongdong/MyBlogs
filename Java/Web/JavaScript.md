#JavaScprit
1. Javascript 是网景(Netscape)公司开发的一种基于客户端浏览器、~~基于~~**面向对象**、事件驱动式的网页脚本语言。前身叫做LiveScript。
2. JavaScrpit声明变量使用Var与不使用Var的区别：定义在函数外的变量一定是全局变量；定义在函数内的变量，如果声明了 var，那么该变量就是局部变量，<font color=red>**如果不声明var，那么该变量就是全局变量**</font>。
```javascript
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
3. 

