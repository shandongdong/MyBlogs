# JSP与Servlet
## 语法
### JSP 中的声明和脚本段
1. 新建test1.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%--i的值是一个声明--%>
    <%! int i = 3;%>
    <% int j = 3; %>    <%-- j的值是一个脚本段 -->


<%= i-- %><br/>
<%= j-- %><br/>

<h1> 刷新页面多次查看变化</h1>
</body>
</html>
```
2. 查看.\apache-tomcat-9.0.12\work\Catalina\localhost\java-web-servlet\org\apache\jsp下生成的test1_jsp.java文件
3. JSP声明格式“<%! int a = 3; %>”,脚本段格式“<% int b = 3; %>”,当服务器将JSP转换为Servlet时，会将JSP中的声明转换为类的成员变量，而将脚本段转换为方法中的局部变量，Servlet是单实例的，这样成员变量的值就只有一个，每个用户都会访问到它，而脚本段中的值是局部变量，每个用户访问的时候各有一份，互不影响。

### JSP forword指令
- <jsp:forword>指令用于转向页面，在该指令后面的所有代码都没有机会执行了。可以通过JSP转换后的java源码发现执行转向代码会后代码中直接就“return”了，所以后面的代码没有执行。

## JSP内置对象
- 内置对象映射表

对象名 |	类型	| 作用域
--- | --- | ---
request	    | javax.servlet.ServletRequest	    | Request
response	| javax.servlet.ServletResponse	    | Page
pageContext	| javax.servlet.jsp.PageContxt	    | Page
session	    | javax.servlet.http.HttpSession	| Session
application	| javax.servlet.ServletContext	    | Application
out	        | javax.servlet.jsp.JspWriter	    | Page
config	    | javax.servlet.ServletConfig	    | Page
page	    | java.lang.Object	                | Page
exception	| java.lang.Throwable	            | Page
### request 对象
1. request的setAttribute()与getAttribute()方法都是在服务端内部执行的，客户端不知道服务端是否执行过这两个方法。
2. request的getParameter()方法作用是获取客户端通过表单或url请求参数所发过来的参数值，是客户端与服务端之间的交互。 
3. request的对象内的数据存活范围就是在request对象的存活范围，当客户端向服务端发送一个请求，服务端向客户端返回一个响应后，该请求对象就被销毁了。
4. 

### session 对象
> https://blog.csdn.net/qq_15096707/article/details/71081313
- Session是服务端技术，Cookies是客户端技术。Session对象由Tomcat服务器为我们创建，开发者使用时通过“HttpSession session = request.getSession();”获取Session对象。
#### Servlet中使用Session
1. 新建SessionDemo01.java
```java
@WebServlet(name = "SessionDemo01", urlPatterns = "/SessionDemo01")
public class SessionDemo01 extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.getSession();

        response.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        String url1 = response.encodeURL("/java-web-servlet/SessionDemo02"); // 通过encodeURL方法为该请求增加Session ID的参数；如前面getSession方法是通过Cookie获取到Session ID的，则这里不会增加Session ID这个参数，因为服务器可以判断用户浏览器未禁用Cookie。如果是首次创建Session同样也会添加Session ID参数。
        String url2 = response.encodeURL("/java-web-servlet/SessionDemo03");
        out.print("<a href='" + url1 + "'>购买</a>   ");
        out.print("<a href='" + url2 + "'>结账</a>");
    }
}
2. SessionDemo02.java
```java
@WebServlet(name = "SessionDemo02", urlPatterns = "/SessionDemo02")
public class SessionDemo02 extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        HttpSession session = request.getSession();
        session.setAttribute("name", "笔记本");
        System.out.println(this.getClass().getName() + ":" + session.getId());
    }
}
```
3. SessionDemo03.java
```java
@WebServlet(name = "SessionDemo03", urlPatterns = "/SessionDemo03")
public class SessionDemo03 extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");

        HttpSession session = request.getSession();
        response.getWriter().write((String) session.getAttribute("name"));
        System.out.println(this.getClass().getName() + ":" + session.getId());

    }
}
```
4. 浏览器访问：http://localhost:8080/java-web-servlet/SessionDemo01
5. 点击购买时，流程流转到SessionDemo02处理，向session对象中赛入一个键值对后。点击结账是服务端从Session对象中拿到设置的值。
#### JSP中使用Session、
1. session1.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="session2.jsp">
    姓名：<input type="text" name="username">
    <input type="submit" value="提交">
</form>
</body>
</html>
```
2. session2.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<%
    String name = request.getParameter("username");

    session.setAttribute("logName", name);  //将信息放置到session中
%>
你的名字："<%= name %>"已经写入session<br/>
<a href="session3.jsp">check</a>
</body>
</html>
```
3. session3.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    String yourName = (String) session.getAttribute("logName"); //从session中获取key为logName的对象
    if (null == yourName) {
        System.out.println("您还未登录");
%>
"您还未登录"
<%
} else {
    System.out.println("你已经登录：" + yourName);
%>
你已经登录：<%= yourName %>
<%
    }
%>
</body>
</html>
```

### application 对象
- 新建applicatin.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<%
    if (application.getAttribute("counter") == null) {
        application.setAttribute("counter", "1");
        System.out.println("if");
    } else {
        String str = application.getAttribute("counter").toString();
        int i = Integer.valueOf(str).intValue();
        i++;
        application.setAttribute("counter", Integer.toString(i));
        System.out.println("else");
    }
%>
您是第<%= application.getAttribute("counter") %> 位访问者!
</body>
</html>
```
- 存活范围最大的一个对象，只要服务器没有关闭，application对象中的数据就会一直存在。在整个服务器运行过程中，application对象只有一个。
- 可以通过修改上面程序中的“application”为“session、request”来查看三个对象的存活范围。
### 总结
- request、session、application这3个对象的范围是逐个增加的：request只在一个请求的范围内对象有效；session是在浏览器窗口内范围有效；application则是在整个服务器运行期间，只要不重启对象都存在。
- application.getRealPath("/result.jsp")这个方法会返回资源在服务器上的绝对路径。

## 请求转发
- RequestDispatcher 的forward(request, response)发放称作请求转发。
1. myForward.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<form action="MyForwardServlet">
    username:<input type="text" name="username">
    <input type="submit" value="submit">
</form>
</body>
</html>
```
2. MyForwardServlet.java
```java
@WebServlet(urlPatterns = "/MyForwardServlet")
public class MyForwardServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String username = request.getParameter("username");
        request.setAttribute("username", username);
        RequestDispatcher requestDispatcher = request.getRequestDispatcher("myResult.jsp");   //将请求转发到下一个组件
        requestDispatcher.forward(request, response);   //将请求，响应对象传递给下一个组件进行处理。所以下个组件处理时还是在同一个请求内
    }
}
```
3. myResult.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<%= request.getAttribute("username") %>
</body>
</html>
```
## 请求重定向
- HttpServletResponse对象的 sendRedirect(String location)方法称作重定向。如果location地址前面加上“/”，则表示相对于 Servlet 容器的跟来请求，即http://localohost:8080，如果location地址前面没有加上“/”，则表示相对于当前请求的URI来寻找地址。
1. mySendRedirect.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<form action="MySendRedirect">
    username:<input type="text" name="username">
    <input type="submit" value="submit">
</form>
</body>
</html>
```
2. MySendRedirect.java
```java
@WebServlet(urlPatterns = "/MySendRedirect")
public class MySendRedirect extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //请求转发
        RequestDispatcher requestDispatcher = request.getRequestDispatcher("myResultSendRedirect.jsp");
        requestDispatcher.forward(request, response);

        //请求重定向
        response.sendRedirect("myResultSendRedirect.jsp");
    }
}
```
3. myResultSendRedirect.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
username:<%= request.getParameter("username") %>
</body>
</html>
```

## 请求转发和请求重定向区别
- 请求转发处理流程：当浏览器向服务器发送一个请求-->服务器接受到能处理请求的Servlet-->服务器产生一个request、response对象-->这个两个对象分别赋值给Servlet的doGet的方法的参数，由Servlet来处理由浏览器发送本次请求所产生的对象-->转发到结果页面,返回结果给客户端。整个过程处于同一个请求中。
![](http://cloudnotes.nos-eastchina1.126.net/20181112100235-6538.jpg)
- 请求重定向处理流程：当浏览器向服务器发送一个请求-->服务器接受到能处理请求的Servlet-->当执行到“response.sendRedirect()”方法时-->直接return不继续向下执行了-->返回一个响应给客户端，并携带参数告诉客户端接下来需要访问方法指定的组件-->客户端收到响应后立即向服务端重新发送一个请求，请求地址为sendRedirect("myResultSendRedirect.jsp")括号中的组件“myResultSendRedirect.jsp”-->服务器找到myResultSendRedirect.jsp文件后开始执行它-->返回结果给客户端。整个过程处于二个请求中。
![](http://cloudnotes.nos-eastchina1.126.net/20181112100948-219921.jpg)
- 通过代码可以观察出两者的区别。 request.getRequestDispatcher()这个是通过请求对象来调用的，属于请求对象的方法。而response.sendRedirect()是响应对象来调用的，响应一旦调用那么请求就结束了，开始客户度返回执行结果。
- 也可以通过浏览器插件network、地址栏查看请求过程。
![](http://cloudnotes.nos-eastchina1.126.net/20181112102356-32501.jpg)

## JSP与JavaBean
- JavaBean是一种可重复使用、 且跨平台的软件组件.JavaBean使得HTML与Java程序分离，这样便于维护代码。 
- 在jsp中使用JavaBean
1. 创建一个Person类，并生产对应的getXxx()，setXxx()方法。
```java
public class Person {
    private String name;
    private int age;
    private String address;
}
```
2. javabean.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page import="com.github.shandongdong.servlet.bean.Person" %>
<html>
<body>

<jsp:useBean id="person" class="com.github.shandongdong.servlet.bean.Person"></jsp:useBean>

<jsp:setProperty name="person" property="name" value="zhangsan"></jsp:setProperty>
<jsp:setProperty name="person" property="age" value="18"></jsp:setProperty>
<jsp:setProperty name="person" property="address" value="HangZhou"></jsp:setProperty>

<jsp:getProperty name="person" property="name"></jsp:getProperty>
<br/>
<jsp:getProperty name="person" property="age"></jsp:getProperty>
<br/>
<jsp:getProperty name="person" property="address"></jsp:getProperty>
<br/>
<%--上面使用标签方式生产Person对象与下面脚本段方式其实是相同的--%>
<%
    Person person1 = new Person();
    person1.setName("lisi");
    out.println(person1.getName());
%>

<%--使用param参数动态赋值，在请求javabean.jsp时可以通过传递参数?helloworld=welcmoe方式来动态给name属性赋值--%>
<jsp:setProperty name="person" property="name" param="helloworld"></jsp:setProperty>
<br/>
<jsp:getProperty name="person" property="name"></jsp:getProperty>
<br/>
</body>
</html>
```
3. 访问http://localhost:8080/java-web-servlet/javabean.jsp?helloworld=aaa
