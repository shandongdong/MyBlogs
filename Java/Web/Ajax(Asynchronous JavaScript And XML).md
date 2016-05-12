#Ajax(Asynchronous JavaScript And XML)<font color=#FF0000>异步</font>的JavaScript与XML
* Ajax中的一个重要对象是 XMLHttpRequest。
* Ajax异步请求数据例子：
创建一个页面
```html
<!DOCTYPE html>
<html>
  <head>
    <title>ajax.html</title>
    <script type="text/javascript">

    var xmlHttpRequest = null;    //声明一个空对象以接收XMLHttpRequest对象

    var ajaxSubmit = function()
    {
        //IE与其他浏览器的实现方式不同，IE通过ActiveX控件方式，其他浏览器默认提供支持
        //alert(window.ActiveXObject);
        if(window.ActiveXObject)    // IE浏览器
        {
            xmlHttpRequest = new ActiveXObject("Microsoft.XMLHTTP");
        }
        else if(window.XMLHttpRequest)    //如果浏览器中存在XMLHttpRequest对象
        {
            xmlHttpRequest = new XMLHttpRequest();
        }

        if(null != xmlHttpRequest)
        {
            /**
             * 使用Ajax准备向服务器发送请求：
             * 第一个参数：HTTP请求的方法，一般用大写表示;
             * 第二个参数：请求的资源地址;
             * 第三个参数：Boolean值，true为异步方式发送请求;
             */
            xmlHttpRequest.open("GET", "AjaxServlet", true);

            // 使用Ajax想服务器发送请求后，对服务器的响应做处理的方式一般是通过回调的方式
            xmlHttpRequest.onreadystatechange = ajaxCallBack;    // 当状态改变时函数得到执行

            /**
             * 真正想服务器发送数据
             * 如果已GET方式发送请求,send()参数可以不写或者写null，
             * 如果已POST方式发送请求,send()参数里面写发送的请求体内容。
             */
            xmlHttpRequest.send(null);
        }

    }

    // 处理响应
    function ajaxCallBack()
    {
        //alert("ajax 的回调函数被调用");
        /**
         * 当状态一旦发生变化回调得到调用，一共会被调用4次。
         * Ajax本身也是基于Http协议模式基于请求响应的这种模式
         * 1: 刚构造完对象，还没有发送请求;
         * 2: 请求发送过去了，还没收到响应;
         * 3: 响应来了，但还没处理完;
         * 4: 请求也发过去了，响应也回来了。不管请求对于错
         *
         */
         if(xmlHttpRequest.readyState == 4)
         {
             if(xmlHttpRequest.status == 200)    // Http响应的状态是OK的
             {
                 var responseText = xmlHttpRequest.responseText;    //服务器端传回来的值

                 document.getElementById("div1").innerHTML = responseText;
             }
         }
    }
    </script>
  </head>
  <body>
    <input type="button" value="get content from server" onclick="ajaxSubmit();" />
    <div id="div1"></div>
  </body>
</html>
```
创建一个Servlet
```java
public class AjaxServlet extends HttpServlet
{
    int i = 0;
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException
    {
        PrintWriter out = resp.getWriter();
        System.out.println("doGet invoked!");
        out.println(i++);
        out.flush();
    }
}
```
